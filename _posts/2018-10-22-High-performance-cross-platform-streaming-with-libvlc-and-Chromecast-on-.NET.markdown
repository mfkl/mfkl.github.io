---
# layout: post
title:  "High performance, cross-platform media streaming with libvlc and Chromecast"
date:   2018-10-21 10:10:40 +0700
categories: chromecast
classes: wide
---
With the release of [libvlc 3.0](https://www.videolan.org/vlc/releases/3.0.0.html), casting your media to your TV is finally possible with libvlc (and the VLC apps). In this post, I will show you how to cast a media using LibVLCSharp, in a cross-platform way.

But before I do so, there are a few prerequisites that you should know regarding VLC's Chromecast module, as it is a bit _special_. To quote [Geoffrey Metais](https://geoffreymetais.github.io/features/vlc-30/), the [Android VLC port](https://code.videolan.org/videolan/vlc-android) maintainer:

> [...] VLC is 100% Open Source and Chromecast SDK isn’t: We had to develop our very own Chromecast stack by ourselves. This is also why there is no voice actions for VLC (except with Android Auto), we cannot use Google Play Services.

> Furthermore, Chromecast is not designed to play local video files: When you watch a Youtube video, your phone is just a remote controller, nothing more. Chromecast streams the video from youtube.com.
That’s where it becomes complicated, Chromecast only supports very few codecs number, let’s say h264. Google ensures that your video is encoded in h264 format on youtube.com, so streaming is simple.
With VLC, you have media of any format. So VLC has to be a http server like youtube.com, and provide the video in a Chromecast compatible format. And of course in real time, which is challenging on Android because phones are less powerful than computers [...].

##### Note: if you want to have a look at how it works under the hood, [here](https://github.com/videolan/vlc-3.0/tree/master/modules/stream_out/chromecast) is a good place to start.

So I can cast my DVDs to my Chromecast, that's cool... but how do I make use of this from .NET, on any platform?

Let's have a look at the code.

The first step is to find a Chromecast on our local network.

~~~~csharp
void DiscoverChromecasts()
{
    // load native libvlc libraries
    Core.Initialize();

    // create core libvlc object
    _libVLC = new LibVLC();

    // create a renderer discoverer
    _rendererDiscoverer = new RendererDiscoverer(_libVLC);

    // register callback when a new renderer is found
    _rendererDiscoverer.ItemAdded += RendererDiscoverer_ItemAdded;

    // start discovery on the local network
    _rendererDiscoverer.Start();
}
~~~~

So when a new RendererItem (typically a chromecast, or other distant renderers) has been discovered, the `ItemAdded` event is raised.

~~~~csharp
void RendererDiscoverer_ItemAdded(object sender, RendererDiscovererItemAddedEventArgs e)
{
    WriteLine($"New item discovered: {e.RendererItem.Name} of type {e.RendererItem.Type}");

    if (e.RendererItem.CanRenderVideo)
        WriteLine("Can render video");
    if (e.RendererItem.CanRenderAudio)
        WriteLine("Can render audio");

    // add newly found renderer item to local collection
    _rendererItems.Add(e.RendererItem);
}
~~~~

We will now start casting a media. In this example, I will use a HTTP URL, but of course local content works as well (using a crossplatform native file picker, for instance).

~~~~csharp
void StartCasting()
{
    // create a media
    var media = new Media(_libVLC,
        "http://www.quirksmode.org/html5/videos/big_buck_bunny.mp4",
        Media.FromType.FromLocation);

    // create the mediaplayer
    _mediaPlayer = new MediaPlayer(_libVLC);

    // set the first discovered renderer item (chromecast) on the mediaplayer
    // if you set it to null, it will start to render normally (i.e. locally) again
    _mediaPlayer.SetRenderer(_rendererItems.First());

    // start the playback
    _mediaPlayer.Play(media);
}
~~~~

The full sample is available on the [VideoLAN GitLab](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/Chromecast).

It is currently implemented for both iOS and Android using Xamarin.Forms. It will however work on any platform supported by libvlc and Mono/.NET.