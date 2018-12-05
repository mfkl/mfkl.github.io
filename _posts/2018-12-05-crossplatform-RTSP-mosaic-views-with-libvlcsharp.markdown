---
layout: post
title:  "Cross-platform RTSP Mosaic views with LibVLCSharp"
date:   2018-12-05 16:10:40 +0700
categories: libvlc rtsp xamarin forms
---

# Cross-platform RTSP Mosaic views with LibVLCSharp

VLC and the LibVLC library handle many different streaming formats and protocols to read media from any source. One of them is called Real Time Streaming Protocol (RTSP). According to the [VideoLAN Wiki](https://wiki.videolan.org/RTSP/):

> The RTSP (Real Time Streaming Protocol) protocol is a client-server multimedia presentation control protocol, designed to address the needs for efficient delivery of streamed multimedia over IP networks. It leverages existing web infrastructure (for example, inheriting authentication and PICS from HTTP) and works well both for large audiences as well as single-viewer media-on-demand.

A common use case is an IP camera which uses RTSP for live streaming.

Let's see how succinctly we can build a fully cross-platform Xamarin.Forms RTSP player with a mosaic of 4 views using [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp).

For the UI, we will divide a grid in 4 equally sized views. A simple way to do this in XAML would be

~~~~xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoMosaic"
             xmlns:shared="clr-namespace:LibVLCSharp.Forms.Shared;assembly=LibVLCSharp.Forms"
             x:Class="VideoMosaic.MainPage">
    <Grid x:Name="MainGrid">
        <Grid.RowDefinitions>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition  Width="*"/>
            <ColumnDefinition  Width="*"/>
        </Grid.ColumnDefinitions>
        <shared:VideoView x:Name="VideoView0" Grid.Row="0" Grid.Column="0"/>
        <shared:VideoView x:Name="VideoView1" Grid.Row="0" Grid.Column="1"/>
        <shared:VideoView x:Name="VideoView2" Grid.Row="1" Grid.Column="0"/>
        <shared:VideoView x:Name="VideoView3" Grid.Row="1" Grid.Column="1"/>
    </Grid>
</ContentPage>
~~~~

Ok, our `Grid` is setup with 4 cross-platform *native* libvlc `VideoView`.

For playback, we need to instantiate a `LibVLC` object, 4 `MediaPlayer` objects (one for each `VideoView`) and 4 `Media` objects which represent the RTSP streams.

~~~~csharp
public partial class MainPage : ContentPage
{
    const string VIDEO_URL = "rtsp://184.72.239.149/vod/mp4:BigBuckBunny_175k.mov";
    readonly LibVLC _libvlc;

    public MainPage()
    {
        InitializeComponent();

        // this will load the native libvlc library (if needed, depending on the platform). 
        Core.Initialize();

        // instantiate the main libvlc object
        _libvlc = new LibVLC();
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        // create mediaplayer objects,
        // attach them to their respective VideoViews
        // create media objects and start playback

        VideoView0.MediaPlayer = new MediaPlayer(_libvlc);
        VideoView0.MediaPlayer.Play(new Media(_libvlc, VIDEO_URL, Media.FromType.FromLocation));

        VideoView1.MediaPlayer = new MediaPlayer(_libvlc);
        VideoView1.MediaPlayer.Play(new Media(_libvlc, VIDEO_URL, Media.FromType.FromLocation));

        VideoView2.MediaPlayer = new MediaPlayer(_libvlc);
        VideoView2.MediaPlayer.Play(new Media(_libvlc, VIDEO_URL, Media.FromType.FromLocation));

        VideoView3.MediaPlayer = new MediaPlayer(_libvlc);
        VideoView3.MediaPlayer.Play(new Media(_libvlc, VIDEO_URL, Media.FromType.FromLocation));
    }
}
~~~~

It is safe to create as many `MediaPlayer` objects as you want, from a single `LibVLC` object.

iOS and Android screenshots:

<img src="/assets/mosaic-ios.png" width="320" />
<img src="/assets/mosaic-android.png" width="320" />

This sample is available on the [VideoLAN GitLab](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/VideoMosaic)