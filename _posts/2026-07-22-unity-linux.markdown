---
title: "Introducing VLC for Unity - Linux Edition"
date: 2026-07-22
classes: wide
---

VLC for Unity 2026-3 is out, and Linux is now officially supported in VLC for Unity!

The 2026-3 release brings native Linux x86_64 support to both the Unity Editor and Standalone builds, on X11 and Wayland.

It has been tested on Ubuntu and will work with most Linux distributions.

This is a first: VLC for Unity is the first cross-platform Unity video plugin to land native Linux support!

## Native video playback on Linux

Under the hood, VLC for Unity uses OpenGL rendering through GLX and EGL, with DMA-BUF texture sharing to pass video frames efficiently to Unity's renderer.

VLC for Unity gives Linux applications and games access to VLC's extensive playback capabilities through [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) and LibVLC. This includes broad codec and container support, high-resolution video and network playback using protocols such as HLS and RTSP.

The initial Linux release targets x86_64 and OpenGL. ARM64 and Vulkan support are planned for future releases.

<p align="center">
    <a href="https://videolabs.io/store/unity"><img src="/assets/unity-logo.png" alt="VLC for Unity"/></a>
</p>
