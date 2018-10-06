---
layout: post
title:  "Introducing LibVLCSharp"
date:   2018-10-06 10:10:40 +0700
categories: libvlc intro
---

# Introducing LibVLCSharp

LibVLCSharp are .NET/Mono bindings for `libvlc`, the multimedia framework powering the VLC applications.

We support all .NET runtimes (Xamarin/Mono, .NET Core and .NET Framework) on most platfoms by targeting .NET Standard 2.0.

`libvlc` is *modularized* into [hundreds of plugins](https://github.com/mfkl/libvlc-nuget/blob/master/tree.md), which may be loaded at runtime. This architecture provides great flexibility to developers (both VLC devs and devs consuming the library). The unified, complete and (somewhat) high level `libvlc` C API allows a wide range of operations, such as:

- Network browsing for distant filesystems (SMB, FTP, SFTP, NFS...).
- HDMI passthrough for Audio HD codecs, like E-AC3, TrueHD or DTS-HD.
- Stream to distant renderers, like Chromecast.
- 360 video and 3D audio playback with viewpoint change.
- Support for Ambisonics audio and more than 8 audio channels.
- Subtitles size modification live.
- Hardware decoding and display on all platforms.
- DVD playback and menu navigation.

```csharp
        static void Main(string[] args)
        {
            // Loading the native libvlc library
            Core.Initialize();

            using (var libVLC = new LibVLC())
            using (var mediaPlayer = new MediaPlayer(libVLC))
            {
                mediaPlayer.Play(new Media(libVLC, "http://www.quirksmode.org/html5/videos/big_buck_bunny.mp4", Media.FromType.FromLocation));
                Console.ReadKey();
            }
        }
```

LibVLCSharp aims to always support the latest `libvlc` major version (currently version 3).

We are distributing packages (native and managed) through [nuget](https://www.nuget.org/profiles/videolan).

We use [GitLab](https://code.videolan.org/videolan/LibVLCSharp) and [GitHub](https://github.com/videolan/libvlcsharp).