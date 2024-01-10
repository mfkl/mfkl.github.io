---
title:  "Unity's Open-Source Double Standard: the ban of VLC"
date:   2024-01-10 11:10:40 +0700
classes: wide
---

# VLC for Unity integration

For the readers unaware, we started distributing binaries on the Unity Store for the open-source [VLC for Unity](https://code.videolan.org/videolan/vlc-unity) integration back in December 2019.

The integration essentially was a bridge between the Unity game engine and the VLC multimedia engine, allowing to build your own media-player based on VLC technology in Unity-based games. Both Unity, through Mono, and LibVLC are highly portable so this is a compelling argument for this cross-platform integration.

Since the start, we have had many users downloading the assets from the Unity Store for their Unity apps and games when requiring demanding multimedia solutions. We had 3 assets targeting specific platforms:
- Windows,
- UWP,
- Android.

# Unity Store ban

This all changed at the end of the summer 2023 when Unity emailed us the following:

<p align="center">
    <img src="/assets/unity-store-email.png"/>
</p>

And just like this, our [publisher account was instantly banned](https://assetstore.unity.com/publishers/39987).

After _months_ of slow back-and-forth over email trying to find a compromise, including offering to exclude LGPL code from the assets, Unity basically told us we were not welcome back to their Store, ever. _Even if we were to remove all LGPL code from the Unity package_.

Where it gets fun is that there are currently hundreds if not thousands of Unity assets that include LGPL dependencies (such as FFmpeg) in the Store **right now**. Enforcement is seemingly totally random, unless you get reported by someone, apparently.

It gets better... Unity itself, both the Editor and the runtime (which means _your shipped game_) **is already using LGPL dependencies**! Unity is built on libraries such as Lame, libiconv, libwebsockets and websockify.js (at least). Full list of open-source Unity dependencies [here](https://gist.github.com/mfkl/ad5cbeadf144e52a762a09fac6a05a70).

So Unity gets to use and benefit from LGPL open-source libraries, games built with Unity depend on LGPL code by default (hello glibc!), but publishers and Unity users are not allowed to do so through the Unity Store?

# Introducing the [Videolabs Store](https://videolabs.io/store)

Numerous companies make use of the LibVLC SDK and other related technologies (like FFmpeg). 

Sometimes they will run into issues or request a new feature and while the community can sometimes help, the limited time of a few volunteers only goes so far. I have written about [OSS sustainability before](https://mfkl.github.io/2020/10/25/OSS-sutainability.html) and that is very much on topic here.

It is in the best interest of both open-source project maintainers and commercial consumers to have a clear products and services offering for a given project, and that is what we have created with the [Videolabs Store](https://videolabs.io/store) for both LibVLC and FFmpeg.

<p align="center">
    <a href="https://videolabs.io/store"><img src="/assets/vlabs-store-1.png"/></a>
</p>

If you are a company requiring multimedia products or consulting for your own projects, this store will be of interest to you.

### Flexible multimedia consulting packages

The Videolabs team is composed of VLC and FFmpeg experts in most protocols, formats and platforms.

If you are using or planning to use LibVLC or FFmpeg in your project and need help, whether it be custom builds, bug fixes, SDK integration or simply answers to your questions for your specific needs, these packages are for you!

<p align="center">
    <a href="https://videolabs.io/store"><img src="/assets/vlabs-store-2.png"/></a>
</p>

We have created 3 multimedia consulting packages: 3 hours, 10 hours and 24 hours. They can be purchased for a one-time service or a monthly subscription.

No matter which OS platform or toolkit you are building with, we can help.

### Other products

The [LibVLCSharp commercial license](https://videolabs.io/store/libvlcsharp/) and the [LibVLC ebook](https://mfkl.gumroad.com/l/libvlc-good-parts) can also be found in the [Videolabs Store](https://videolabs.io/store), as well as other upcoming products such as Kyber, our new ultra low latency game/desktop streaming and remote control SDK, and more game engine integration such as Unreal. 

The [VLC Unity integration](https://videolabs.io/store/unity/) can of course also be purchased from our Store. Stay tuned for more Unity platforms support coming later this month!

<p align="center">
    <a href="https://videolabs.io/store"><img src="/assets/vlabs-store-3.png"/></a>
</p>
