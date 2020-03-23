---
layout: post
title:  "Multimedia streaming and torrents with LibVLCSharp and MonoTorrent"
date:   2020-03-23 11:10:40 +0700
categories: libvlc
---

### Multimedia streaming and torrents with LibVLCSharp and MonoTorrent

This post explores possibilities of torrenting and streaming technologies in .NET by combining interesting features from several libraries to make a unique and fun CLI sample app. First, some context.

## The BitTorrent protocol

According to [Wikipedia](https://en.wikipedia.org/wiki/BitTorrent),
> BitTorrent (abbreviated to BT) is a communication protocol for peer-to-peer file sharing (P2P) which is used to distribute data and electronic files over the Internet.

> BitTorrent is one of the most common protocols for transferring large files, such as digital video files containing TV shows or video clips or digital audio files containing songs. Peer-to-peer networks have been estimated to collectively account for approximately 43% to 70% of all Internet traffic (depending on location) as of February 2009. In February 2013, BitTorrent was responsible for 3.35% of all worldwide bandwidth, more than half of the 6% of total bandwidth dedicated to file sharing.

Since the BitTorrent is so well suited to transfer large files (such as multimedia files), integration with mediaplayers like VLC has commonly been an area of interest for a while.

<p align="center">
    <img src="/assets/file-sharing.png"/>
</p>

## Popular torrenting libraries

- The most popular BitTorrent protocol implementation is [libtorrent](https://github.com/arvidn/libtorrent/), written in C++. This will be relevant later in the post.

- Currently maintained by [Alan McGovern](https://github.com/alanmcgovern), [MonoTorrent](https://github.com/alanmcgovern/monotorrent) seems to be the obvious choice for .NET. Do note that it is not merely a binding over `libtorrent` but a complete C# implementation of the BitTorrent protocol.

## Torrent support in VLC

Integrating torrent support for VLC is not a new idea, and there are several ways to go about it. Here are 2 possible approaches:

1. Write a VLC plugin.

    Since VLC has been designed from early on with a plugin-based architecture, one could [write a plugin](https://wiki.videolan.org/Hacker_Guide/How_To_Write_a_Module/) for it, usually using C/C++ or Rust.

    That is the approach that [Johan Gunnarsson](https://github.com/johang) chose with [vlc-bittorrent](https://github.com/johang/vlc-bittorrent). It uses [libtorrent](https://github.com/arvidn/libtorrent/), the C++ BitTorrent implementation mentioned above and hooks into VLC at the plugin interface. 

    The VLC core would load the plugin and raise its callbacks.

    A downside of this approach is that you have to build and link the native plugin for each platform you want to support.

2. Use the LibVLC API.

    LibVLC is a more high level interface to access the VLC features. It abstracts away intricacies of the APIs to provide a simple yet powerful crossplatform API. This is what LibVLCSharp is based on and what I went with for this sample. But first, let's have a look at recent developments of MonoTorrent.

    In pull request [271](https://github.com/alanmcgovern/monotorrent/pull/271), a new media streaming API has just been introduced by Alan:

    > Added MonoTorrent.Streaming.StreamProvider. 

    > This class allows files in a torrent to be accessed using a readable and seekable Stream while the Torrent is still being downloaded. The Stream instance returned by StreamProvider.CreateStreamAsync uses a special PiecePicker so that it downloads data sequentially starting at the current Stream.Position. 

    > In other words - if you are streaming a video file you can seek to the middle of the file and MonoTorrent will immediately start downloading pieces from that point onwards…

    It sounds like exactly what we need to get this working smoothly with LibVLC.

    On the LibVLC side, the `Media` type has a constructor which takes a .NET stream through the [StreamMediaInput](https://github.com/videolan/libvlcsharp/blob/3.x/LibVLCSharp/Shared/StreamMediaInput.cs) type.

    The idea is then to feed a `.torrent` file (local or from a URL) of our choosing to MonoTorrent, start its engine and get a stream from it.

    With the .NET `Stream`, create a LibVLCSharp `Media` object and start the playback.

<p align="center">
    <img src="/assets/lvst.gif"/>
</p>
<p align="center">
    <i>LVST CLI app</i>
</p>


This way we can watch the movie seamlessly as we download it onto the device using the BitTorrent protocol. And with MonoTorrent and LibVLCSharp both targeting .NET Standard, this works on pretty much **all** platforms.

## Local or remote playback

For local playback, we have a couple solutions. 
- By default, if running from a CLI app, the VLC engine will create a new Window that will be used for video output.
- We could integrate this feature with the crossplatform [MediaElement](http://localhost:4000/libvlc/crossplatform/xamarin/forms/2019/08/13/MediaPlayerElement-Plug-and-play-LibVLCSharp-UI-video-control.html) control.
- Playing in any VLC app would also be possible by having this CLI app stream to to `localhost:port`, and play that stream in VLC.

But... VLC also supports chromecast, and so, [LibVLCSharp does too](https://mfkl.github.io/chromecast/2018/10/21/High-performance-cross-platform-streaming-with-libvlc-and-Chromecast-on-.NET.html).

> Would it be possible to send the movie stream to the local chromecast while downloading it from the P2P network with MonoTorrent? 

**Yes**, and VLC will also transcode the media in real time if the chromecast does not natively support it.

## LVST

[LVST](https://github.com/mfkl/lvst) is a CLI .NET Core 3.1 app running on Linux, macOS and Windows. Xamarin support would be trivial to add, but I wanted this to be a CLI sample.

It is designed to be a simple sample. Just provide it with a `.torrent` file URL with `--torrent` and it will start to download and play it. Enabling the chromecast is as easy as giving it the `--cast` flag.

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