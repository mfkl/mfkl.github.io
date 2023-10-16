---
title:  "Milestone: 2 million downloads for VideoLAN .NET NuGet packages"
date:   2023-07-10 11:10:40 +0700
classes: wide
---

[The NuGet VideoLAN account](https://www.nuget.org/profiles/videolan) just reached **2 million downloads** in the .NET ecosystem.

<p align="center">
    <img src="/assets/2-million.png"/>
</p>

As we reach this new arbitrary milestone, I'd like to reflect on the current LibVLC ecosystem developments and what is coming next for our users.

# LibVLC 4 support

We are working hard towards the support of LibVLC 4 in LibVLCSharp. This includes surfacing new native APIs and modifying existing ones, while keeping in mind ease of use and .NET conventions for a seamless developer experience across all supported platforms. 

We also work closely with core VLC developers to provide feedback and help shape the upcoming LibVLC APIs that will enable new capabilities for your applications. A couple of examples of new exciting LibVLC 4 APIs (already/eventually available in all programming languages through LibVLC bindings) include:

- A new mediaplayer recording API

```c
/**
 * Start/stop recording
 *
 * \note The user should listen to the libvlc_MediaPlayerRecordChanged event,
 * to monitor the recording state.
 *
 * \version LibVLC 4.0.0 and later.
 *
 * \param p_mi media player
 * \param enable true to start recording, false to stop
 * \param dir_path path of the recording directory or NULL (use default path),
 * has only an effect when first enabling recording.
 */
LIBVLC_API void libvlc_media_player_record( libvlc_media_player_t *p_mi, 
                                            bool enable, const char *dir_path);
```

- A new advanced mediaplayer custom video rendering GPU API.

This rather complex LibVLC API allows consumers to handle the hardware accelerated rendering themselves, instead of pointing LibVLC to a Window handle. Performant game engine integration, such as Unity3D and Unreal, is one of the key use cases of this new LibVLC 4.0 API.

```c
/**
 * Set callbacks and data to render decoded video to a custom texture
 *
 * \warning VLC will perform video rendering in its own thread and at its own rate,
 * You need to provide your own synchronisation mechanism.
 *
 * \param mp the media player
 * \param engine the GPU engine to use
 * \param setup_cb callback called to initialize user data
 * \param cleanup_cb callback called to clean up user data
 * \param resize_cb callback to set the resize callback
 * \param update_output_cb callback to get the rendering format of the host (cannot be NULL)
 * \param swap_cb callback called after rendering a video frame (cannot be NULL)
 * \param makeCurrent_cb callback called to enter/leave the rendering context (cannot be NULL)
 * \param getProcAddress_cb opengl function loading callback (cannot be NULL for \ref libvlc_video_engine_opengl and for \ref libvlc_video_engine_gles2)
 * \param metadata_cb callback to provide frame metadata (D3D11 only)
 * \param select_plane_cb callback to select different D3D11 rendering targets
 * \param opaque private pointer passed to callbacks
 *
 * \note the \p setup_cb and \p cleanup_cb may be called more than once per
 * playback.
 *
 * \retval true engine selected and callbacks set
 * \retval false engine type unknown, callbacks not set
 * \version LibVLC 4.0.0 or later
 */
LIBVLC_API
bool libvlc_video_set_output_callbacks( libvlc_media_player_t *mp,
                                        libvlc_video_engine_t engine,
                                        libvlc_video_output_setup_cb setup_cb,
                                        libvlc_video_output_cleanup_cb cleanup_cb,
                                        libvlc_video_output_set_window_cb window_cb,
                                        libvlc_video_update_output_cb update_output_cb,
                                        libvlc_video_swap_cb swap_cb,
                                        libvlc_video_makeCurrent_cb makeCurrent_cb,
                                        libvlc_video_getProcAddress_cb getProcAddress_cb,
                                        libvlc_video_frameMetadata_cb metadata_cb,
                                        libvlc_video_output_select_plane_cb select_plane_cb,
                                        void* opaque );
```

> The version 4 of LibVLC is still in development, so the API is not frozen yet, but the core functionality is already quite stable and used by many clients on all platforms, as well as the Unity integrations.

## Unity

We recently shipped support for the [UWP platform in VLC Unity](https://mfkl.github.io/2023/04/17/unity-uwp.html), enabling the use of LibVLCSharp and LibVLC in Microsoft HoloLens devices, desktop and Xbox platforms, in addition to the existing [Android](https://videolabs.io/store/unity) and [Windows](https://videolabs.io/store/unity) classic targets.

As we are finalizing support for VLC Unity on iOS and macOS, we will then focus on general developer experience improvements as well as documentation efforts to solidify the Unity integration.

The feedback we have received so far is great: the video decoding performance is unmatched and VLC Unity is one of the most capable and advanced media player asset currently available on the Videolabs Store in terms of features.

## Uno Platform

While we initially released a [LibVLCSharp / Uno integration](https://platform.uno/blog/vlc-windows-lamp-now-supported-uno-platform) back in 2019, it has not been updated for a while to keep up with the latest Uno releases and accompanying API changes. 

This may all change soon, now that [Uno decided to use LibVLC for their Linux mediaplayer support](https://platform.uno/blog/new-release-media-player-element-on-mobile-web-linux-webview2-support).

We are looking forward to work together to improve the developer experience for .NET developers in the multimedia space across all platforms that Uno and VLC support.

## Commercial licensing and consulting offering

Exactly 2 years ago, we introduced the [LibVLCSharp Commercial License](https://videolabs.io/products/libvlcsharp) in an effort to secure the long term maintenance of the project. I firmly believe companies should be put to contribution when it comes to opensource sustainability, not individuals, and I have written [about this topic before](https://mfkl.github.io/2020/10/25/OSS-sutainability.html).

If your company relies on LibVLCSharp for their commercial products, we encourage you to support the project and help secure the long term maintenance by [purchasing a commercial license today](https://videolabs.io/products/libvlcsharp).

For more general multimedia needs, such as consulting, training, specific LibVLC / FFMPEG features or bug fixes, or even new platforms support, [Videolabs](https://videolabs.io) is the partner of choice with experts on all platforms.

# LibVLC community update

## LibVLC Discord server

Almost 3 years ago, we created the [LibVLC Discord Server](https://discord.gg/3h3K3JF) to foster the LibVLC community with both LibVLC users and bindings maintainers, **for all 12 supported programming languages**.

The Discord server has grown steadily to **1200+ members** and it is a place where LibVLC users can get support from other members of the LibVLC community. Bindings maintainers also help each others out at times.

When someone has a cool use case or sample they want to share with the community, they can post in the _showcase_ channel on the LibVLC Discord. For example, that is how I learned about the existence of the [VLC support in Minecraft](https://github.com/MinecraftMediaLibrary/EzMediaCore), using the awesome [VLCJ bindings](https://github.com/caprica/vlcj) from [Caprica](https://github.com/caprica).

<p align="center">
    <video width="640" height="360" src="https://user-images.githubusercontent.com/40838203/132433665-a675fc35-e31f-4044-a960-ce46a8fb7df5.mp4" controls="controls">
    </video>
</p>

## Zig

The latest cool kid on the block, [ziglang](https://ziglang.org), made an appearance in the VLC community with [libvlc-zig](https://github.com/kassane/libvlc-zig) which allows Zig developers to enjoy a compelling developer experience while using LibVLC and Zig. We are looking forward to see the apps you build with Zig and LibVLC!

While [libvlc-zig](https://github.com/kassane/libvlc-zig) focuses on the LibVLC API, it is also possible to use Zig to build native VLC plugins using the lower level VLC API, such as this [vlc-mixer example](https://github.com/lachie/vlc-mixer).

Stay tuned.