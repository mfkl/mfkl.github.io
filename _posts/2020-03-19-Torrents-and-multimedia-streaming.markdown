---
layout: post
title:  "Torrents and multimedia streaming with LibVLCSharp and MonoTorrent"
date:   2020-03-23 11:10:40 +0700
categories: libvlc
---

### Torrents and multimedia streaming with LibVLCSharp and MonoTorrent

This post explores possibilities of torrenting and streaming technologies in .NET by combining key features from several libraries to build a unique and fun CLI sample app. But first, some context.

## The BitTorrent protocol

The BitTorrent protocol is a protocol used to share files between users over internet.

According to [Wikipedia](https://en.wikipedia.org/wiki/BitTorrent),
> BitTorrent (abbreviated to BT) is a communication protocol for peer-to-peer file sharing (P2P) which is used to distribute data and electronic files over the Internet.

> BitTorrent is one of the most common protocols for transferring large files, such as digital video files containing TV shows or video clips or digital audio files containing songs. Peer-to-peer networks have been estimated to collectively account for approximately 43% to 70% of all Internet traffic (depending on location) as of February 2009. In February 2013, BitTorrent was responsible for 3.35% of all worldwide bandwidth, more than half of the 6% of total bandwidth dedicated to file sharing.

Since the BitTorrent is so well suited to transfer large files (such as multimedia files), the idea of embedding it in mediaplayers like VLC has commonly been an area of interest for some time.

<p align="center">
    <img src="/assets/file-sharing.png"/>
</p>

## Popular torrenting libraries

A few words on torrenting libraries you can use in your code.

- The most popular BitTorrent protocol implementation is [libtorrent](https://github.com/arvidn/libtorrent/), written in C++. We will come back to it later in this blog post.

- Currently maintained by [Alan McGovern](https://github.com/alanmcgovern), [MonoTorrent](https://github.com/alanmcgovern/monotorrent) seems to be the obvious choice for .NET. Do note that it is not merely a binding over `libtorrent` but a complete C# implementation of the BitTorrent protocol.

## Torrenting with VLC

Integrating torrent support for VLC is not a new idea, and there are several ways to go about it. Here are 2 possible approaches:

1. Write a VLC plugin.

    Since VLC has been designed from early on with a plugin-based architecture, one could [write a plugin](https://wiki.videolan.org/Hacker_Guide/How_To_Write_a_Module/) for it, usually using C/C++ or Rust.

    That is the approach that [Johan Gunnarsson](https://github.com/johang) chose with [vlc-bittorrent](https://github.com/johang/vlc-bittorrent). [vlc-bittorrent](https://github.com/johang/vlc-bittorrent) uses [libtorrent](https://github.com/arvidn/libtorrent/), the C++ BitTorrent implementation mentioned above and hooks into VLC at the plugin interface level. 

    The VLC core would load the plugin and raise its callbacks.

    A downside of this approach is that you must build and link the native plugin for each platform you want to support.

2. Use the LibVLC API.

    LibVLC is a higher level interface to access the VLC features. It abstracts away intricacies of the APIs to provide a simple yet powerful crossplatform API. This is what LibVLCSharp is based on and what I chose to use for this sample. To continue, let's have a look at recent developments of MonoTorrent.

    In the pull request [271](https://github.com/alanmcgovern/monotorrent/pull/271), Alan introduced a new media streaming API:

    > Added MonoTorrent.Streaming.StreamProvider. 

    > This class allows files in a torrent to be accessed using a readable and seekable Stream while the Torrent is still being downloaded. The Stream instance returned by StreamProvider.CreateStreamAsync uses a special PiecePicker so that it downloads data sequentially starting at the current Stream.Position. 

    > In other words - if you are streaming a video file you can seek to the middle of the file and MonoTorrent will immediately start downloading pieces from that point onwards…

    It sounds like exactly what we need to get torrenting working smoothly with LibVLCSharp. With this API, we can feed a `.torrent` file to MonoTorrent and get back a .NET `Stream`.
    
    This .NET `Stream` is representing the media content, which is being downloaded in real time from the BitTorrent network.

    On the LibVLC side, the `Media` type has a constructor which takes a .NET stream using the [StreamMediaInput](https://github.com/videolan/libvlcsharp/blob/3.x/LibVLCSharp/Shared/StreamMediaInput.cs) type.

    With the .NET `Stream` from MonoTorrent, it is then possible to create a LibVLCSharp `Media` object and finally start the playback.

<p align="center">
    <img src="/assets/lvst.gif"/>
</p>
<p align="center">
    <i>LVST CLI app</i>
</p>


This way we can watch the movie seamlessly as we download it onto the device using the BitTorrent protocol. And with MonoTorrent and LibVLCSharp both targeting .NET Standard, this works on pretty much **all** platforms.

## Local or remote playback

#### Local
For local playback, we have a couple solutions. 
- By default, if running from a CLI app, the VLC engine will create a new Window that will be used for video output.
- We could use the crossplatform [MediaElement](http://localhost:4000/libvlc/crossplatform/xamarin/forms/2019/08/13/MediaPlayerElement-Plug-and-play-LibVLCSharp-UI-video-control.html) control and combine it to this.
- Playing in any VLC app would also be possible by having this CLI app stream to the `localhost:port`, and play that stream in VLC.

#### Remote

But... VLC also supports chromecast, and so, [LibVLCSharp does too](https://mfkl.github.io/chromecast/2018/10/21/High-performance-cross-platform-streaming-with-libvlc-and-Chromecast-on-.NET.html).

> Would it be possible to send the movie stream to the local chromecast while downloading it from the P2P network with MonoTorrent? 

**Yes**, and VLC will also transcode the media in real time if the chromecast does not natively support it.

## LVST

[LVST](https://github.com/mfkl/lvst) is a CLI .NET Core 3.1 app running on Linux, macOS and Windows. Xamarin support would be simple to add, but I wanted this to be a CLI sample.

[LVST](https://github.com/mfkl/lvst) is designed to be a basic sample and focused on the torrenting feature, there are currently no playback controls.

Just provide it with a `.torrent` file URL with `--torrent` and it will start to download and play it. Casting to the chromecast is as easy as giving it the `--cast` flag.

```
.\LVST.exe --help
LVST 1.0.0
Copyright (C) 2020 LVST

  -v, --verbose    Set output to verbose messages.

  -t, --torrent    The torrent link to download and play

  -c, --cast       Cast to the chromecast

  -s, --save       Whether to save the media file. Defaults to true.

  -p, --path       Set the path where to save the media file.
```

Feel free to fork away if you'd like to play around with [LVST](https://github.com/mfkl/lvst).

## Possible improvements

What else could be done with regard to LibVLCSharp and MonoTorrent?

- Make mobile samples
- In case of multiple chromecasts on the network, allow selecting one chromecast from the CLI.
- Integrate this with the MediaElement control.

I am also considering making a `LibVLCSharp.MonoTorrent` package to make torrent support transparent in LibVLCSharp. Feel free to reach out if that is something you would like to see!
