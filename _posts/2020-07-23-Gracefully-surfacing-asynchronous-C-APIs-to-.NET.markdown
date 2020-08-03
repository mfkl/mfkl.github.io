---
# layout: post
title:  "Gracefully surfacing asynchronous C APIs to .NET"
date:   2020-07-23 11:10:40 +0700
categories: libvlc
classes: wide
---
.NET was one of the first ecosystem to offer the `async/await` pattern to simplify writing asynchronous code, if not [the first](https://softwareengineering.stackexchange.com/a/377514). It has since been added to many other programming languages, such as Python, JavaScript, Rust and more.

When it comes to the LibVLC API, many functions are explicitly documented as asynchronous, because they perform various (potentially) long running processing work.

With this blogpost, I want to explore how I surfaced an asynchronous C API from the preview LibVLC 4 version, as gracefully as possible, to the .NET world.

## Generating a thumbnail with LibVLC 4 in C

LibVLC 4 provides several new APIs, amongst which [`libvlc_media_thumbnail_request_by_time`](https://www.videolan.org/developers/vlc/doc/doxygen/html/group__libvlc__media.html#ga96594d719fc20364a2cbc0981877a55e). 

This function allows the caller to generate a **thumbnail** (or image) from any video file. This can be very useful when you want to have a mosaic of _preview_ images in your UIs for example, like the YouTube main page does.

> For brevity, I will only list the C function names (but the docs/code is OSS so feel free to have a look on [git](https://code.videolan.org/videolan/vlc)) for the simplest use case.

So, creating a `Media` is the necessary first step.

~~~~c
libvlc_media_new_location
~~~~

Next, we want to subscribe to a `MediaThumbnailGenerated` LibVLC event. The generated thumbnail will be placed in the published event. Event subscription is done through this LibVLC function:

~~~~c
libvlc_event_attach
~~~~

And finally, we can call 
~~~~c
libvlc_media_thumbnail_request_by_time
~~~~

If successful, the callback registered with the event attach call will contain the thumbnail information.

# Thumbnailer .NET usage

Let's have a quick look at the API from a .NET user standpoint, with these 2 short lines of C# using LibVLCSharp 4.

~~~~csharp
using var media = new Media(libVLC, new Uri("C:\\Path\\To\\Videos\\big_buck_bunny_720p_30mb.mkv"));
using var thumbnail = await media.GenerateThumbnail(time: 200, 
                                speed: ThumbnailerSeekSpeed.Fast, 
                                width: 200, 
                                height: 200, 
                                crop: false, 
                                pictureType: PictureType.Argb);
~~~~

Several things are going on here.

- Both the `media` and `thumbnail` objects are created with the `using` C# 8 keyword, meaning that they will be automatically freed (and their native counterpart as well) whenever going out of scope.
- `async/await` is used to kickstart the asynchronous operation of generating the thumbnail, we will get back to this later in more details.
- Various parameters are provided to customize the thumbnail generation. 

The actual LibVLCSharp API definition looks like this
~~~~csharp
/// <summary>Start an asynchronous thumbnail generation.
/// If the request is successfuly queued, the MediaThumbnailGenerated event is 
/// guaranteed to be emited.
/// </summary>
/// <param name="time">The time at which the thumbnail should be generated</param>
/// <param name="speed">The seeking speed</param>
/// <param name="width">The thumbnail width</param>
/// <param name="height">The thumbnail height</param>
/// <param name="crop">Should the picture be cropped to preserve aspect ratio</param>
/// <param name="pictureType">The thumbnail picture type</param>
/// <param name="timeout">A timeout value in ms, or 0 to disable timeout</param>
/// <param name="cancellationToken">The cancellation token needed 
/// to cancel the thumbnail generation
/// </param>
/// <returns>A valid Picture object or null in case of failure</returns>
public async Task<Picture> GenerateThumbnail(long time, ThumbnailerSeekSpeed speed,
                uint width, uint height, bool crop, PictureType pictureType, long timeout = 0, 
                CancellationToken cancellationToken = default)
~~~~

Notice the last 2 optional parameters, with default values, allowing to smoothen the final public API. We will now explore the actual interop part, including how the timeout and cancellation are handled with regard to the native C API.

# Thumbnailer .NET interop

This is the actual fun part!

~~~~csharp
var tcs = new TaskCompletionSource<Picture>();
~~~~

Using the `TaskCompletionSource`, we are able to navigate the asynchronous flow of the method and decide on the outcome returned to the .NET caller depending on the native library result.

~~~~csharp
ThumbnailGenerated += OnThumbnailGenerated;
~~~~

This .NET event registration performs a `libvlc_event_attach` call (as seen at the beginning of the post) under the hood. The published event contains the thumbnailer data buffer and information and is set onto the `TaskCompletionSource`. In case of timeout given the specified timeout value, the thumbnail is `null` and the task is canceled. 

~~~~csharp
void OnThumbnailGenerated(object sender, 
                            MediaThumbnailGeneratedEventArgs mediaThumbnailGeneratedEventArgs)
{
    if (mediaThumbnailGeneratedEventArgs.Thumbnail != null)
        tcs.TrySetResult(mediaThumbnailGeneratedEventArgs.Thumbnail);
    else
        tcs.TrySetCanceled();
}
~~~~

We then kickstart the asynchronous operation, like so:
~~~~csharp
var result = Native.LibVLCMediaThumbnailRequestByTime(NativeReference, time, speed, width, 
                                                            height, crop, pictureType, timeout);
request = new ThumbnailerRequest(result);

await tcs.Task.ConfigureAwait(false);
~~~~

If the thumbnail generation is canceled by the user from .NET using the cancellation token, we unsubscribe immediately as well as let the native library (LibVLC) know and set the task as cancelled. The `async/await` state machine will handle the rest for us and return control to the caller.

~~~~csharp
var cancellationTokenRegistration = cancellationToken.Register(() =>
{
    ThumbnailGenerated -= OnThumbnailGenerated;
    if (request.Valid)
        Native.LibVLCMediaThumbnailRequestCancel(request.NativeReference);
    tcs.TrySetCanceled();
});
~~~~

Method code in full:

~~~~csharp
async Task<Picture> ThumbnailRequestInternal(CancellationToken cancellationToken = default)
{
    cancellationToken.ThrowIfCancellationRequested();
    ThumbnailerRequest request = default;

    var tcs = new TaskCompletionSource<Picture>();
    var cancellationTokenRegistration = cancellationToken.Register(() =>
    {
        ThumbnailGenerated -= OnThumbnailGenerated;
        if (request.Valid)
            Native.LibVLCMediaThumbnailRequestCancel(request.NativeReference);
        tcs.TrySetCanceled();
    });

    void OnThumbnailGenerated(object sender, MediaThumbnailGeneratedEventArgs mediaThumbnailGeneratedEventArgs)
    {
        if (mediaThumbnailGeneratedEventArgs.Thumbnail != null)
            tcs.TrySetResult(mediaThumbnailGeneratedEventArgs.Thumbnail);
        else
            tcs.TrySetCanceled();
    }

    try
    {
        ThumbnailGenerated += OnThumbnailGenerated;

        var result = Native.LibVLCMediaThumbnailRequestByTime(NativeReference, time, speed, width, height, crop, pictureType, timeout);
        request = new ThumbnailerRequest(result);

        return await tcs.Task.ConfigureAwait(false);
    }
    finally
    {
        cancellationTokenRegistration.Dispose();
        ThumbnailGenerated -= OnThumbnailGenerated;
        if (request.Valid)
            Native.LibVLCMediaThumbnailRequestDestroy(request.NativeReference);
    }
}
~~~~

`GenerateThumbnail` then finally returns a `Picture` object to the .NET user and they can save it to a path on disk, or do further processing on the raw buffer using great .NET 2D graphics library like [SkiaSharp](https://github.com/mono/SkiaSharp) or [ImageSharp](https://github.com/SixLabors/ImageSharp), for example.

To recap, using only the following 2 lines of C# code:

~~~~csharp
using var media = new Media(libVLC, new Uri("C:\\Path\\To\\Videos\\big_buck_bunny_720p_30mb.mkv"));
using var thumbnail = await media.GenerateThumbnail(time: 200, 
                                speed: ThumbnailerSeekSpeed.Fast, 
                                width: 200, 
                                height: 200, 
                                crop: false, 
                                pictureType: PictureType.Argb);
~~~~

The following 8 native calls have been handled for the LibVLCSharp user transparently.

~~~~c
libvlc_media_new_location
libvlc_event_attach
libvlc_media_thumbnail_request_by_time
libvlc_media_thumbnail_request_cancel (if needed)
libvlc_media_thumbnail_request_destroy
libvlc_event_detach
libvlc_picture_release
libvlc_media_release
~~~~

As usual, thanks to the great portability of both LibVLC and .NET, this code is fully crossplatform (Windows, macOS, Linux, Android, iOS, tvOS...). 

LibVLCSharp 4 is set to be officially released along LibVLC 4 in the near future, but preview builds are already available.