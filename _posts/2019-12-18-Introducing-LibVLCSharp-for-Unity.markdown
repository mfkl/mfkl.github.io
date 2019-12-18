---
layout: post
title:  "Introducing LibVLCSharp for Unity3D"
date:   2019-12-18 11:10:40 +0700
categories: libvlc unity
---

<p align="center">
    <img src="/assets/unity-cover-image.png"/>
</p>

### Today, we are announcing the initial release of [VLC for Unity (Windows)](https://assetstore.unity.com/packages/tools/video/vlc-for-unity-windows-133979) on the Unity Store!

This Unity plugin allows you to use a LibVLC-powered video player in your games and Unity apps. Whether you need to support a strange format, live streaming or play a 4K video in your latest production, we got you covered.

## Commercial support
While this Unity plugin is [opensource](https://code.videolan.org/mfkl/vlc-unity/), its Unity Store price is **100USD**.

This price is intended to help sponsor the development and the commercial support of this Unity plugin by [Videolabs](https://videolabs.io), the main commercial contributor to the VLC apps.

## All LibVLC features available in your Unity game

Given that this plugin is using [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) (which uses LibVLC), it exposes more or less the [same feature set](https://code.videolan.org/videolan/LibVLCSharp#features) and same codecs support than VLC, such as:

- Plays [all formats](https://wiki.videolan.org/VLC_Features_Formats/)
- Network browsing for distant filesystems (SMB, FTP, SFTP, NFS...).
- HDMI pass-through for Audio HD codecs, like E-AC3, TrueHD or DTS-HD.
- Stream to distant renderers, like Chromecast.
- 360 video and 3D audio playback with viewpoint change.
- Support for Ambisonics audio and more than 8 audio channels.
- Subtitles live size modification.
- Hardware and software decoding on all platforms.
- DVD playback and menu navigation.
- Equalizer support

And more.

## Performance focus

This plugin uses a **LibVLC 4.0** build which has seen a lot of improvements regarding performance in the past year. The plugin also makes use of a new, low-level, LibVLC API consisting of [Zero Copy](https://en.wikipedia.org/wiki/Zero-copy) GPU callbacks.

This means your (slow) CPU is bypassed and only your GPU is working to provide decoded textures. The LibVLC API offers Direct3D and OpenGL hooks, but this is all taken care for you inside the Unity plugin. Have a look [here](https://code.videolan.org/mfkl/vlc-unity/blob/master/Assets/VLC-Unity-Windows/Plugins/Source/RenderAPI_D3D11.cpp) if you're curious about it.

With this plugin and this new API, you can get similar performance to [VLC](https://www.videolan.org/) Desktop in your Unity games and enjoy low CPU use and optimized battery life on your devices when playing high definition medias.

## Getting started

As usual, you need to load the libvlc libraries and create your LibVLC, MediaPlayer and Media objects as you would on any other platform with [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp).

The public API of this plugin is strictly the same than LibVLCSharp for all but one additional method on the **MediaPlayer**:

~~~~csharp
/// <summary>
/// Retrieve a video frame from the Unity plugin.
/// </summary>
/// <param name="updated">True if the video frame has been updated</param>
/// <returns>A decoded texture</returns>
public IntPtr GetFrame(out bool updated)
~~~~

This is the method that you need to call in a [Unity coroutine](https://docs.unity3d.com/Manual/Coroutines.html) in your script and give its result to your [texture](https://docs.unity3d.com/ScriptReference/Texture2D.UpdateExternalTexture.html)'s `UpdateExternalTexture` method. Full script example can be found [here](https://code.videolan.org/mfkl/vlc-unity/blob/master/Assets/VLC-Unity-Windows/Scripts/UseRenderingPlugin.cs).

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

<p align="center">
    <img src="/assets/unity-logo.png"/>
</p>
