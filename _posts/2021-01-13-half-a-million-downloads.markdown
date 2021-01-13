---
title:  "Milestone: Half a million downloads for VideoLAN packages in the .NET ecosystem"
date:   2021-01-13 11:10:40 +0700
classes: wide
---

[The NuGet VideoLAN account](https://www.nuget.org/profiles/videolan) just reached half a million downloads in the .NET ecosystem. 

<p align="center">
    <a href="https://nugettrends.com/packages?months=24&ids=VideoLAN.LibVLC.Windows&ids=LibVLCSharp&ids=LibVLCSharp.Forms&ids=VideoLAN.LibVLC.iOS&ids=VideoLAN.LibVLC.Android"><img src="/assets/nugettrends.png"/></a>
</p>

The first publication of the first VideoLAN NuGet package dates back to April 1st, 2018 (no joke!). We have been steadily adding new packages since then to cover all platforms supported by both LibVLC and .NET.

Today, we actively publish and maintain 15 NuGet packages, 6 of which  actually containing native platform-specific code. The remaining 9 packages are managed C# DLLs composed of the main binding (LibVLCSharp) and various GUI toolkit integrations for getting users up and running quickly.

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

LibVLCSharp counts about 19 contributors, with most people doing one-off contributions and mainly 2 active maintainers ([Jérémy](https://github.com/jeremyVignelles) and myself). I'd like to thank each of them for their valuable OSS contributions!

# What's next?

Many exciting things! More game engine integrations, ongoing LibVLC 4 support, interop performance improvements and plenty of new integrations, samples and features are yet to be implemented.

We have a diverse and (mostly) well-defined backlog of both features to implement and bugs to fix, with a varying degree of difficulty, and always welcome new contributors. Feel free to reach out on the [LibVLC community Discord server](https://discord.gg/3h3K3JF) (320+ members).

Feel free to let me know what you think on [Twitter](https://twitter.com/martz2804).
