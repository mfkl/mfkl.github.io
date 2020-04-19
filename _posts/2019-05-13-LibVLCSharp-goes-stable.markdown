---
# layout: post
title:  "LibVLCSharp 3.0: The first stable release of VideoLAN's crossplatform .NET binding for LibVLC"
date:   2019-05-13 11:10:40 +0700
categories: libvlc crossplatform xamarin forms release
classes: wide
---
[LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) is the first official **VideoLAN** .NET binding for the multimedia LibVLC library that is _fully cross-platform_. It has been downloaded over 10K times.

It provides a simple cross-platform audio and video API for .NET on all platforms. Its comprehensive multimedia API can be used across mobile, server and desktop to render video and output audio as well as encode and stream.

# What can LibVLCSharp do?

The full libvlc 3.0 set of public C APIs are surfaced as .NET APIs with LibVLCSharp.

You can explore LibVLC features by playing with the official [VLC apps](https://www.videolan.org/vlc) or reading the [Wikipedia page](https://en.wikipedia.org/wiki/VLC_media_player).

The TL;DR is this:

- Plays all audio and video formats
- Network browsing for distant filesystems (SMB, FTP, SFTP, NFS...).
- HDMI pass-through for Audio HD codecs, like E-AC3, TrueHD or DTS-HD.
- Stream to distant renderers, like Chromecast.
- 360 video and 3D audio playback with viewpoint change.
- Support for Ambisonics audio and more than 8 audio channels.
- Subtitles size modification live.
- Hardware and software decoding on all platforms.
- DVD playback and menu navigation.
- Equalizer support.

And a lot more...

# Supports all major platforms

This is the first crossplatform libvlc binding by _VideoLAN_ that is available and integrated with such a wide range of platforms.

LibVLCSharp can now be used on Mac, Linux or Windows to develop applications in C#, F# and VB.NET.

It is integrated with most GUI toolkits such as **Windows Forms**, **WPF**, **UWP**, **Cocoa** and **GTK** so it can be used seamlessly to display video and output audio on **Android**, **iOS**, **macOS**, **tvOS**, **Windows** (including Xbox and Mobile) as well as **Linux** (including Raspberry Pi).

With LibVLCSharp.Forms, we also support Xamarin.Forms for **native crossplatform UI development**.

If you would like to have a quick look at minimal samples, you can find those [here](https://code.videolan.org/videolan/LibVLCSharp/tree/master/Samples).

You could also follow our [Getting Started guide](https://code.videolan.org/videolan/LibVLCSharp/blob/master/GETTING-STARTED.md)

# Versioning

Our versioning story is tied to the libvlc major versions as explained in our [versioning](https://code.videolan.org/videolan/LibVLCSharp/blob/master/VERSIONING.md) document. Other LibVLC bindings also follow that versioning scheme. This explains why the first stable release is not a 1.0 version.

LibVLCSharp 3.x works with LibVLC 3.x. LibVLCSharp 4.x will work with LibVLC 4.x.

# What's next?

- More platform support, notably game engines (Unity, Unreal, Godot...).
- Implement libvlc 4.0 APIs and demo new features.
- Documentation. Since we closely follow the libvlc API you can always refer to the C header for docs, but we are in the process of setting up docs for LibVLCSharp/.NET specific information.
- [MediaElement](https://github.com/kakone/LibVLCSharp.Forms.MediaPlayerElement), a Xamarin.Forms crossplatform full featured MediaElement. Work has been started by one of our contributor, [kakone](https://github.com/kakone), and we are looking for help.

We are always looking for new contributors, so feel free to reach out on our [Gitter](https://gitter.im/libvlcsharp/Lobby) or [GitLab](https://code.videolan.org/videolan/LibVLCSharp) if you are interested :-)

If you would like to contribute, we accept pull requests on both GitHub and GitLab. Please read and follow our [contributing guide](https://code.videolan.org/videolan/LibVLCSharp/blob/master/CONTRIBUTING.md).

As always, you may download the packages from our [NuGet](https://www.nuget.org/profiles/videolan) account.

Thanks to [@JeremyVignelles](https://github.com/jeremyVignelles), [@jsuarezruiz](https://twitter.com/jsuarezruiz), [@mitermite](https://twitter.com/mitermite), [@wjax](https://github.com/wjax) and [@Ch4rg3r](https://github.com/Ch4rg3r).