---
title:  "Introducing VLC for Unity - Android Edition"
date:   2022-05-04 11:10:40 +0700
classes: wide
youtubeId: aF4EMISZf34

---

Today, we are announcing the initial release of [VLC for Unity (Android)](https://videolabs.io/store/unity) on the Videolabs Store!

<p align="center">
  <img src="/assets/unity-courtyard.png"/>
</p>

Seasoned Unity developers will recognize the famous [Courtyard](https://assetstore.unity.com/packages/essentials/tutorial-projects/the-courtyard-49377) Unity scene which was edited to showcase our VLC plugin playing the famous Big Buck Bunny video.

{% include youtubePlayer.html id=page.youtubeId %}

<br/>
This Unity plugin allows you to use a LibVLC-powered video player in your Unity-based Android apps and games. Whether you need to support a rare video format, live streaming, HLS, RTSP or play a 4K video in your latest production, we got you covered. Feel free to [give it a try](https://videolabs.io/solutions/vlc-unity-trial.unitypackage)!

## All LibVLC features available in your Unity game

Given that this plugin is using [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) (which uses LibVLC), it exposes more or less the [same feature set](https://code.videolan.org/videolan/LibVLCSharp#features) and same codecs support than VLC, such as:

- Play every media file formats, every codec and every streaming protocols
- Run on every platform, from desktop (Windows, Linux, Mac) to mobile (Android, iOS) and TVs
- Hardware and efficient decoding on every platform, up to 8K
- Network browsing for distant filesystems (SMB, FTP, SFTP, NFS...) and servers (UPnP, DLNA)
- Playback of Audio CD, DVD and Bluray with menu navigation
- Support for HDR, including tonemapping for SDR streams
- Audio passthrough with SPDIF and HDMI, including for Audio HD codecs, like DD+, TrueHD or DTS-HD
- Support for video and audio filters
- Support for 360 video and 3D audio playback, including Ambisonics
- Able to cast and stream to distant renderers, like Chromecast and UPnP renderers.

And more!

## Hardware-accelerated video playback in your Android Unity apps and games

The 4.0 development version of LibVLC provides a powerful API which allows to perform custom rendering yet retaining hardware accelerated decoding.

In the context of Unity, this means using VLC for Unity allows you to use video frames as GPU textures in your Unity games. For Android, this solution is based on OpenGL ES.

This is as simple to use for you as writing

```csharp
MediaPlayer.GetTexture(out texture);
```

and uploading it to Unity for post-processing in your game scene.

The CPU architectures supported on Android by VLC Unity are as follows:

- armeabi-v7a,
- arm64-v8a,
- x86,
- x86_64.

## New Unity demo scenes in the VLC for Unity asset

We have recently added several new Unity scenes into the VLC asset. These are shipped with the VLC Unity asset from the Videolabs Store. They provide an efficient way to get started quickly and showcase real use case scenarios for your apps and games.

<img width="100%" src="/assets/3d-scene-unity.png">

This is a basic cinema room with a bit of light, a cinema screen powered by VLC and comfortable chairs.

<img width="100%" src="/assets/media-element-unity.png">

This screenshot shows an in-Editor run of the VLC Canvas example, essentially a simple MediaElement control with a seekbar and a few playback options. It's great because it shows you how to use the LibVLCSharp APIs and bind them with Unity materials and scripts!

We will add more features to this scene in the future. And more scenes.

## What's next?

The next major platform we will work on supporting is **iOS**. It is already supported by both LibVLCSharp and LibVLC, but the VLC Unity plugin needs some adjustments to support iOS. Feel free to reach out if you are interesting in testing this out!

Other than iOS, we will keep on improving the plugin, continuously fixing bugs and enhancing the developer experience. Deeper Unity Editor integration with LibVLC is planned as well, so that in the future you may customize various playback parameters right from the Unity Editor menus!

Feel free to let me know what you think on [Twitter](https://twitter.com/martz2804).

#### [Download the Free Trial version](https://videolabs.io/solutions)