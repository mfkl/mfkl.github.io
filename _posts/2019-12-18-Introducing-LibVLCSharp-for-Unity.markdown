---
# layout: post
title:  "Introducing LibVLCSharp for Unity3D"
date:   2019-12-18 11:10:40 +0700
categories: libvlc unity
classes: wide
---
### Today, we are announcing the initial release of [VLC for Unity (Windows)](https://assetstore.unity.com/packages/tools/video/vlc-for-unity-windows-133979) on the Unity Store!
<br/>
<p align="center">
    <img src="/assets/unity-cover-image.png"/>
</p>

This Unity plugin allows you to use a LibVLC-powered video player in your games and Unity apps. Whether you need to support a strange format, live streaming or play a 4K video in your latest production, we got you covered.

## Commercial support
While this Unity plugin is [opensource](https://code.videolan.org/videolan/vlc-unity/), its Unity Store price is **100USD**.

This price is intended to help sponsor the development and the commercial support of this Unity plugin by [Videolabs](https://videolabs.io), the main commercial contributor to the VLC apps.

#### [Download the Free Trial version](https://videolabs.io/#unity)

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

And more.

## Performance focus

This plugin uses a **LibVLC 4.0** build which has seen a lot of improvements regarding performance in the past year. The plugin also makes use of a new, low-level, LibVLC API consisting of [Zero Copy](https://en.wikipedia.org/wiki/Zero-copy) GPU callbacks.

This means your (slow) CPU is bypassed and only your GPU is working to provide decoded textures. The LibVLC API offers Direct3D and OpenGL hooks, but this is all taken care of for you inside the Unity plugin. Have a look [here](https://code.videolan.org/videolan/vlc-unity/blob/master/Assets/PluginSource/RenderAPI_D3D11.cpp) if you're curious about it.

With this plugin and this new API, you can get similar performance to [VLC](https://www.videolan.org/) Desktop in your Unity games and enjoy low CPU use and optimized battery life on your devices when playing high definition medias.

## Getting started

As usual, you need to load the libvlc libraries and create your LibVLC, MediaPlayer and Media objects as you would on any other platform with [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp).

The public API of this plugin is strictly the same than LibVLCSharp for all but one additional method on the **MediaPlayer**:

~~~~csharp
/// <summary>
/// Retrieve a video frame from the Unity plugin as a texture.
/// </summary>
/// <param name="updated">True if the video frame has been updated</param>
/// <returns>A decoded texture</returns>
public IntPtr GetTexture(out bool updated)
~~~~

This is the method that you need to call in a [Unity coroutine](https://docs.unity3d.com/Manual/Coroutines.html) in your script and give its result to your [texture](https://docs.unity3d.com/ScriptReference/Texture2D.UpdateExternalTexture.html)'s `UpdateExternalTexture` method. Full script example can be found [here](https://code.videolan.org/videolan/vlc-unity/blob/master/Assets/VLC-Unity-Windows/Scripts/UseRenderingPlugin.cs).

<p align="center">
    <img src="/assets/unity-scene.jpg"/>
</p>

## What's next

### Platforms support

Currently, this plugin only supports Windows Classic 64bit with Direct3D11. We aim to bring support to the following platforms during the coming year:

- Windows 32bit,
- UWP,
- macOS, iOS, tvOS,
- Android (prototype working),
- Linux.

And the following graphics APIs:

- OpenGL ES,
- OpenGL,
- Metal,
- Direct3D9.

We currently have a prototype working on Android with OpenGL ES.

### Other goals

- Provide various easy to use, ready-to-run samples and improved documentation.
- Provide in-Editor MediaPlayer customization, so you can customize LibVLC and playback parameters directly from the Unity Editor.

#### [Download the Free Trial version](https://videolabs.io/#unity)

<p align="center">
    <a href="https://assetstore.unity.com/packages/tools/video/vlc-for-unity-windows-133979"><img src="/assets/unity-logo.png"/></a>
</p>