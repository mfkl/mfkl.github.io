---
title:  "Introducing VLC for Unity - macOS Edition"
date:   2024-03-20 11:10:40 +0700
classes: wide

---

Today, we are announcing the release of [VLC for Unity (macOS)](https://videolabs.io/store/unity) on the Videolabs Store!

This Unity plugin allows you to use a LibVLC-powered video player in your Unity-based macOS apps and games. Whether you need to support a rare video format, live streaming, HLS, RTSP or play a 4K video in your latest production, we got you covered. Feel free to [give it a try](https://videolabs.io/solutions/vlc-unity-trial.unitypackage)!

## All LibVLC features available in your macOS Unity game

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

<p align="center">
    <a href="https://videolabs.io/store/unity"><img src="/assets/unity-logo.png"/></a>
</p>

## Hardware acceleration on macOS with Unity and LibVLC

Hardware acceleration is often a highly requested features for video players. Decoding videos with the GPU allows much more efficient decoding allowing to playback high res samples that the CPU could not, while saving battery life and CPU cycles for other apps.

On macOS, GPU hardware video decoder access is performed through either OpenGL or Metal.

At the time of writing, LibVLC does not _yet_ offer a fully featured and battle-tested Metal-based video output for Apple Platform, only OpenGL is available. 

So how to do? 🤔

Well, [a nice workaround](https://code.videolan.org/videolan/vlc-unity/-/blob/master/PluginSource/RenderAPI_OpenGLCGL.mm) was implemented by [Alexandre Janniaux](https://code.videolan.org/alexandre-janniaux) to be able to perform OpenGL calls within a Metal context, notably using [CVMetalTextureCacheCreateTextureFromImage](https://developer.apple.com/documentation/corevideo/1456754-cvmetaltexturecachecreatetexture). 

This way both LibVLC and Unity constraints are satisfied, and both iOS and macOS on Unity use the same Unity Metal backend.

## Testing VLC in your Unity for macOS app

As of today, the initial [VLC for Unity macOS](https://videolabs.io/store/unity/) release includes both Intel x64 and Apple Silicon ARM64 binaries.

It is possible to build through XCode by generating an XCode project file, or directly generating the final app binary through the Unity Editor. Running VLC Unity in the Unity Editor is also supported, although it can sometimes take a while to load all the libraries. We will be working on speeding that up next.

✨ This is the second Apple platform supported by VLC Unity, after the iOS release last month. Future possible Apple target platforms, depending on your interest, could be tvOS and visionOS! Do reach out if you are interested in any platform not yet supported.

Feel free to let me know what you think on [Twitter](https://twitter.com/martz2804).
