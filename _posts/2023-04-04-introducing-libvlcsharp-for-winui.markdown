---
title:  "Introducing LibVLCSharp for WinUI"
date:   2023-04-04 11:10:40 +0700
classes: wide
---

### Today, we are announcing the initial release of LibVLCSharp for WinUI on [NuGet](https://www.nuget.org/profiles/videolan)
<br/>
<p align="center">
    <img src="/assets/logo-winui.png"/>
</p>

# WinUI

LibVLCSharp has had support for the Universal Windows Platform (UWP) since the early days. Before LibVLCSharp, the work to integrate LibVLC with the UWP platform and make the LibVLC engine work well on it, was pioneered by the now defunct [VLC for WinRT project](https://code.videolan.org/videolan/vlc-winrt).

WinUI is the next evolution of the modern UI toolkit for the Windows desktop after UWP (unfortunately, the Xbox target is not supported with WinUI).

As of **LibVLCSharp version 3.7.0**, building multimedia apps using **WinUI 3** is now supported with LibVLC. Expect the usual goodies such as default hardware decoding enabled.

Both _Packaged_ and _Unpackaged_ WinUI apps are supported with LibVLCSharp.

There are several important caveats and changes from the previous UWP LibVLCSharp support that I will detail below, for users migrating or supporting both UWP and WinUI.

## API Breaking change for existing UWP users

When upgrading to LibVLCSharp version 3.7.0, users currently targeting UWP will need to address a build failure as there was a needed namespace change.

In your XAML files:

```diff
-xmlns:lvs="using:LibVLCSharp.Platforms.UWP"
+xmlns:lvs="using:LibVLCSharp.Platforms.Windows"
```

In your C# files:
```diff
-using LibVLCSharp.Platforms.UWP;
+using LibVLCSharp.Platforms.Windows;
```

This should be quick and as painless as possible to fix as you upgrade to LibVLCSharp 3.7.0 in your UWP applications. The minor version of LibVLCSharp is bumped according to our documented [versioning strategy](https://code.videolan.org/videolan/LibVLCSharp/-/blob/master/docs/versioning.md).

## LibVLC build type

With UWP apps using LibVLCSharp, the user needs to add a special, custom-built LibVLC variant, the [VideoLAN.LibVLC.UWP
](https://www.nuget.org/packages/VideoLAN.LibVLC.UWP) nuget package. This has always been the case and is due to the expectation of the underlying runtime of the UWP platform.

For WinUI targets, the classic Windows LibVLC build, [VideoLAN.LibVLC.Windows](https://www.nuget.org/packages/VideoLAN.LibVLC.Windows), **must** be used and only from version 3.0.18 minimum. Using a UWP LibVLC build will not work. As always, starting from the [official sample app](https://code.videolan.org/videolan/LibVLCSharp/-/tree/3.x/samples/LibVLCSharp.WinUI.Sample) is a good idea.

## Minimum Target Framework Version

The minimal WinUI TFM supported version is `net6.0-windows10.0.17763.0`. Do make sure you target it for your WinUI project (or anything above).

---

As we release this initial support of WinUI on NuGet, please make sure to tell us if you encounter any issue and what apps you build with LibVLCSharp for WinUI!