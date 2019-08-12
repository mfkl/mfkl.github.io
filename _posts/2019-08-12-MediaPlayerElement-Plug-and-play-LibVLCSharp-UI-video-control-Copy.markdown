---
layout: post
title:  "MediaPlayerElement: Plug and play and crossplatform UI video control"
date:   2019-08-13 11:10:40 +0700
categories: libvlc crossplatform xamarin forms
---

Apple and Google already provide MediaPlayer frameworks for iOS and Android developers, but they are quite limited compared to what VLC can do in terms of compatible formats and protocols (amongst other things).

Until now, [LibVLCSharp.Forms](https://code.videolan.org/videolan/LibVLCSharp) was providing you with a Xamarin.Forms `View` with video: the [VideoView](https://code.videolan.org/videolan/LibVLCSharp/blob/master/LibVLCSharp.Forms/Shared/VideoView.cs). You had to implement the usual media player controls and buttons yourself, which was not very convenient when all you want is to get up and running quickly, to focus on your core app functionality (which may not be video playback).

### Enter the MediaPlayerElement

A bit less than a year ago, I opened a new [issue](https://code.videolan.org/videolan/LibVLCSharp/issues/45) to track the requirements and demand for a fully featured video control for LibVLCSharp.Forms.

About 4 months ago, our community member [kakone](https://github.com/kakone) came up with a working implementation of a fully customizable, cross-platform and themeable video control for LibVLCSharp.

<p align="center">
    <img src="/assets/media-element-iphone.png" />
</p>
<p align="center">
    <i>iOS</i>
</p>

LibVLCSharp now offers a MediaPlayerElement control working on both **iOS** and **Android** through Xamarin.Forms with many convenient features and behaviors built-in:

- Fully **customizable** and **themeable** control (can style and hide any element from the control)
- Full Xamarin.Forms implementation - no custom renderer added
- **6 aspect ratios**: 4:3, 16:9, Best Fit, Fit screen, Fill, Original
- Play/Pause
- Seeking/Rewind
- **Buffering** progress: Convenient for network streams and large files
- Seekbar with ellapsed and remaining time (and seeking)
- **Chromecast** discovery and management: Automatically look for chromecast and connect/disconnect to them with a single tap
- **Subtitle** track selection
- **Audio** track selection
- _Android only:_ Auto hide software buttons when watching

## Easy customization

We have built the MediaPlayerElement so that it can cater to many different app needs through easy customization. Here are a few examples:

- Your app is used only for non-pauseable live streams? You can hide the play/pause button (or even the whole playback control element).
- No chromecast in the environment your app runs? Remove the chromecast button and disable remote renderer discovery with a single (databinded) property.
- You find touch gestures impractical to control your player? Turn them off.

The MediaPlayerElement has been designed from the ground up so that you can easily (but extensively) customize your playback experience, on each supported platform.

## Get it today!

#### The MediaPlayerElement is available today in version **3.2.0** of [LibVLCSharp.Forms](https://www.nuget.org/packages/LibVLCSharp.Forms/).

As usual, [NuGet](https://www.nuget.org/profiles/videolan) is the preferred way of installing VideoLAN packages in your .NET projects.

A minimal sample is available to get you up and running instantly [here](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/MediaElement) for iOS and Android.

## What's next?

Below are sone of our plans to improve the `MediaPlayerElement`. If you see something missing or would like to suggest a new feature, feel free to open a [feature request](https://code.videolan.org/videolan/LibVLCSharp/issues) or a [bug report](https://code.videolan.org/videolan/LibVLCSharp/issues)!

- [Improve Track management](https://code.videolan.org/videolan/LibVLCSharp/issues/196) (audio/subs delay, external subtitle selection/download).
- [Icon management](https://code.videolan.org/videolan/LibVLCSharp/issues/178): Currently, the `MediaPlayerElement` control expects you to bundle the [fontawesome icon fonts](https://fontawesome.com/icons) in your app projects by default. This is NOT ideal for a "plug and play" experience. We want this to be handled automatically and transparently for you. If you want to use different icons however, this is already supported.
- [Gesture support](https://code.videolan.org/videolan/LibVLCSharp/issues/181): Allow horizontal and vertical gestures to change brightness, volume and seeking options. Detect 360 videos and enable viewpoint change gestures. I have blogged about this [feature](https://mfkl.github.io/libvlc/360/xamarin/forms/ux/2019/02/12/Fun-with-crossplatform-gestures-and-360-videos.html) before.
- [Add lock UI button](https://code.videolan.org/videolan/LibVLCSharp/issues/180).
- [Add Equalizer UI](https://code.videolan.org/videolan/LibVLCSharp/issues/197).
- Investigate [Picture in Picture](https://code.videolan.org/videolan/LibVLCSharp/issues/200) (in-app and external where possible).
- Add Error message panel.
- Add Currently Playing title label.
- More Xamarin.Forms back-end platform support: Depending on user-demand... UWP, macOS and GTK are possible candidates.

<p align="center">
    <img src="/assets/media-element-android.png"/>
</p>
<p align="center">
    <i>Subtitles selection on Android</i>
</p>

As always, you can find the full code for this on [code.videolan.org](https://code.videolan.org/videolan/LibVLCSharp).

The best and fastest way to get started is to clone our [libvlcsharp-samples](https://code.videolan.org/mfkl/libvlcsharp-samples) repository and start the `MediaElement` sample. Building the samples will pull the necessary VideoLAN packages from [NuGet](https://www.nuget.org/profiles/videolan) for your target platform.

If you would like to contribute a new feature or a bugfix, we accept pull requests on [GitHub](https://github.com/videolan/LibVLCSharp/pulls) and merge requests on [GitLab](https://code.videolan.org/videolan/LibVLCSharp/merge_requests).