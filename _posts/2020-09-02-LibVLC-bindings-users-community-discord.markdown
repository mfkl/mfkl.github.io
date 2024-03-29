---
title:  "Calling all LibVLC bindings maintainers and users!"
date:   2020-09-02 11:10:40 +0700
categories: libvlc
classes: wide
---
Announcing the LibVLC Community Discord Server!

Feel free to [join us](https://discord.gg/3h3K3JF), you are all welcome.

# State of LibVLC bindings

LibVLC is a crossplatform, portable, C library which works across most mobile and desktop platforms. 

Since it is written in C, and that most developers don't write user-facing apps in C, the community has created LibVLC bindings surfacing the LibVLC APIs in various programming languages and ecosystems.

Let's go through some of them.

## By VideoLAN
- [libvlcpp](https://code.videolan.org/videolan/libvlcpp) with C++.
> Notably used by the [medialibrary](https://code.videolan.org/videolan/medialibrary). 
- [vlckit](https://github.com/videolan/vlckit) for Apple platforms using Objective-C/Swift.
> Notably used by [VLC for iOS/tvOS](https://apps.apple.com/us/app/vlc-for-mobile/id650377962). 
- [libvlcjni](https://code.videolan.org/videolan/vlc-android) for Android platforms integrating with JNI using Java/Kotlin.
> Notably used by [VLC for Android](https://play.google.com/store/apps/details?id=org.videolan.vlc). 
- [libvlcsharp](https://code.videolan.org/videolan/LibVLCSharp) for all desktop and mobile platforms using C#/F# with .NET.

## By the community
- [vlcj](https://github.com/caprica/vlcj) for Desktop plaforms using Java.
> Currently used by over 600 OSS projects on GitHub.
- [python-vlc](https://github.com/oaubert/python-vlc) for Desktop platforms using Python.
> Very mature (since 2004).
- [vlc-rs](https://github.com/garkimasera/vlc-rs) A relatively new LibVLC binding for Rust users.
- [libvlc-go](https://github.com/adrg/libvlc-go) A Go binding for LibVLC.

##### Feel free to contact me to add your own binding to this post.

So if you are maintaining a LibVLC binding and/or using LibVLC in your app, [join our LibVLC community discord server](https://discord.gg/3h3K3JF)!

Just ask a moderator to create a channel for your ecosystem if that has not been done already.

## Why Discord?

There are many ways to get in touch with the VideoLAN community, such as the [forum](https://forum.videolan.org/), the [developer mailing list](https://mailman.videolan.org/listinfo/vlc-devel), [StackOverflow](https://stackoverflow.com/questions/tagged/libvlc) or even [IRC](https://wiki.videolan.org/Contact_VideoLAN/#IRC). That last one is mostly aimed at core VLC developers though, which is why a space for LibVLC _consumers_ and bindings maintainers would be a great addition.

With libvlcsharp, we had been using Gitter for a while but due to various bugs and limitations, we decided to move to Discord a while ago. We are very happy with the change so far.

At the time of writing, we are around 100 people in the LibVLC discord, mostly from the .NET ecosystem. 

We are now opening up the server to LibVLC users/binding maintainers from _any and all_ programming languages!

## Why join?

Here are some of the value you could get by joining up the Discord server:

### If you're a LibVLC binding user
- Learn about various community projects,
- Get help to troubleshoot problems from volunteers,
- Engage with the community to start contributing to OSS LibVLC bindings,
- Just hangout!

### If you're a LibVLC binding maintainer
- Learn from other LibVLC binding maintainers with similar native interop challenges to yours,
- Help to align LibVLC APIs across multiple programming languages and ecosystems,
- Get help or advice with code and docs,
- Have a community that can help assist your users,
- Share your experience and findings maintaining a LibVLC binding.

We hope to [see you there](https://discord.gg/3h3K3JF)!