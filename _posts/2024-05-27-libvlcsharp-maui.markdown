---
title:  "Introducing LibVLCSharp for MAUI"
date:   2024-05-27 11:10:40 +0700
classes: wide
---

Following the official deprecation of Xamarin.Forms, we are announcing the [LibVLCSharp integration for MAUI](https://www.nuget.org/packages/LibVLCSharp.MAUI).

# Initial release

Starting from 3.8.5, [LibVLCSharp.MAUI](https://www.nuget.org/packages/LibVLCSharp.MAUI) officially supports iOS (net6.0+) and Android (net7.0) modern .NET MAUI targets.

We have had some trouble with the Android version, since some underlying tooling changed and we found a regression affecting our wanted final deployment structure. But we worked around it for now (or until it gets fixed?).

# What's next for LibVLCSharp.MAUI?

As we get more user feedback and tweak these initial releases, we will be looking into expanding platform support with macOS and Windows (WinUI) support.

Another exciting feature will be to bring back our [MediaElement control](https://mfkl.github.io/libvlc/crossplatform/xamarin/forms/2019/08/13/MediaPlayerElement-Plug-and-play-LibVLCSharp-UI-video-control.html) from Xamarin.Forms ashes, and make it MAUI-ready. This control was very popular as it was plug-and-play and came with many implemented features.

We are always looking for contributors and corporate sponsors for this work. Feel free to reach out if you can help!