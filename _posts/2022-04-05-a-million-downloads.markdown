---
title:  "Milestone: A million downloads for VideoLAN packages in the .NET ecosystem"
date:   2022-04-05 11:10:40 +0700
classes: wide
---

[The NuGet VideoLAN account](https://www.nuget.org/profiles/videolan) just reached **1 million downloads** in the .NET ecosystem.

<p align="center">
    <img src="/assets/1-million.png"/>
</p>

Today, we actively publish and maintain **16 NuGet packages**, 6 of which actually containing native platform-specific code. The remaining 10 packages are managed C# DLLs composed of the main binding ([LibVLCSharp](https://www.nuget.org/packages/LibVLCSharp/)) and various GUI toolkit integrations for getting users up and running quickly.

- Xamarin.Android
- Xamarin.iOS
- Xamarin.tvOS
- Xamarin.Mac (Cocoa)
- Windows Classic (WPF, WinForms, GTK)
- Windows Universal (UWP for Desktop, mobile and Xbox)
- Linux including desktop, server and Raspberry Pi (GTK)
- Xamarin.Forms
- Uno Platform (UWP, Android, iOS)
- Avalonia (Windows, macOS, Linux)
- Eto Forms

Both [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) and [LibVLC for NuGet](https://code.videolan.org/videolan/libvlc-nuget) received PRs from a few new contributors recently. They helped out on adding support for new GUI frameworks like Eto, perfecting the WPF VideoView integration and making sure the LibVLC native library plays nice with the latest .NET SDK! I'd like to thank each of them for their valuable OSS contributions.

# On-going work

LibVLC 4 is actively being worked on by the core VLC team and we can expect several new LibVLC APIs to surface in LibVLCSharp in the coming months. I am keeping a [personal wishlist for LibVLC features](https://github.com/mfkl/libvlc-feature-requests) on my side and I try to work on it when time allows. If you would like to see a specific LibVLC feature come to life, feel free to reach out!

Plans to investigate LibVLCSharp interop performance further have been made and it will be exciting to leverage new interop performance features from .NET 5/6 with LibVLC.

# Commercial licensing

Almost a year ago, we introduced **[Commercial Licenses for LibVLCSharp](https://videolabs.io/solutions/libvlcsharp/)** through dual licensing (current OSS licenses remain). This was the start of an experiment to try and make the project financially sustainable. I don't believe user donations are a working solution for most OSS maintainers, and if companies contributed financially to the sustainability of their dependencies, I believe the open-source ecosystem would be more _balanced_.

<p align="center">
    <img src="/assets/mrr-lvs-twitter.png"/>
</p>

After a needed pricing change, and almost a year in, we now stand at $224 monthly recurring revenue with 11 happy customers and growing! This is certainly better than the donation route but there surely is a way to go still.

If your company uses LibVLCSharp in commercial products, please consider supporting the project by [purchasing a commercial license](https://videolabs.io/solutions/libvlcsharp)!

Feel free to let me know what you think on [Twitter](https://twitter.com/martz2804).
