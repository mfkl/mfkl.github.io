---
layout: post
title:  "PulseMusic - music player design with Skia and LibVLC"
date:   2018-12-31 11:10:40 +0700
categories: libvlc skia xamarin forms ux
---

# PulseMusic - stylish music player with Skia and LibVLC

In February 2015, [Anish Chandran](https://twitter.com/canish91), a Microsoft UX and Visual designer, posted on [Dribble](https://dribbble.com/shots/1939991-Music-Player-App-WIP) and [Behance](https://www.behance.net/gallery/23846315/Pulse-Music-App-Identity-UI-UX) an original music player UX concept.

In August 2018, [Javier Suárez Ruiz](https://github.com/jsuarezruiz) implemented and [published on GitHub](https://github.com/jsuarezruiz/PulseMusic) a Xamarin.Forms (iOS/Android) UI implementation of the audio player view of the PulseMusic concept by Anish.

<p align="center">
    <img src="/assets/pulse-music-concept.gif" />
</p>

As mentioned in the [readme](https://github.com/jsuarezruiz/PulseMusic):

> The main objective of the sample is to show the creation of the Player's UI (circular progress, rotating cover, etc.). This App is NOT a real player.

So... let's turn it into a real player with [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp).

This is an extract from the existing [PlayerView.xaml](https://github.com/jsuarezruiz/PulseMusic/blob/master/src/PulseMusic/PulseMusic/Views/PlayerView.xaml):

~~~~xml
<controls:CircleProgress 
    Grid.Row="0"
    VerticalOptions="FillAndExpand"
    HorizontalOptions="FillAndExpand"
    Progress="{Binding Progress}"
    LineBackgroundColor ="{StaticResource BlackColor}"
    ProgressColor="{StaticResource PlayerRedColor}"
    StrokeWidth="12"
    Margin="12"/>
<buttonCircle:CircleButton
    Command="{Binding PlayCommand}"
    FontIcon="FontAwesome"
    Icon="{Binding Icon}" 
    FontSize="{StaticResource FontSize16}"
    TextColor="{StaticResource WhiteColor}" 
    HeightRequest="60" 
    WidthRequest="60" 
    BackgroundColor="{StaticResource PlayerRedColor}"
    HorizontalOptions="Center"
    VerticalOptions="Center" />
<Grid
    Grid.Row="1"
    Margin="70, -24, 70, 0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition Width="Auto" />
    </Grid.ColumnDefinitions>
    <Label 
        Grid.Column="0"
        Text="{Binding StartTime, Converter={StaticResource TimeSpanToStringConverter}}"
        Style="{StaticResource TimeTextStyle}"/>
    <controls:ToggleButton
        Grid.Column="1"
        Checked="False"
        Animate="False"
        CheckedImage="playonce_on"
        UnCheckedImage="playonce_off"/>
    <controls:TapImage
        Grid.Column="2"
        Source="shuffle"/>
    <Label 
        Grid.Column="3"
        Text="{Binding RemainTime, Converter={StaticResource TimeSpanToStringConverter}}"
        Style="{StaticResource TimeTextStyle}"/>
</Grid>
~~~~

The `Binding` XAML keyword indicates the value is databinded to the corresponding ViewModel. For the demo code, Javier had used an artificial countdown to make the UI seem alive (ticks for position and time). Let's hook up a real player!

Xamarin.Forms has a pub/sub concept called [MessagingCenter](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/messaging-center), we will use it to propagate LibVLCSharp's playback events in the app.

~~~~csharp
readonly MediaPlayer _mp;
const string URL = "https://archive.org/download/ImagineDragons_201410/imagine%20dragons.mp4";

public void Init()
{
    // create a libvlc media
    _mp.Media = new Media(_libVLC, URL, Media.FromType.FromLocation);

    // disable video output, we only need audio
    _mp.Media.AddOption(":no-video");

    // subscribe to libvlc playback events
    _mp.TimeChanged += TimeChanged;
    _mp.PositionChanged += PositionChanged;
    _mp.LengthChanged += LengthChanged;
    _mp.EndReached += EndReached;
    _mp.Playing += Playing;
    _mp.Paused += Paused;
}

// when the libvlc mediaplayer events fire, publish an event with the MessagingCenter
private void PositionChanged(object sender, MediaPlayerPositionChangedEventArgs e) =>
    MessagingCenter.Send(MessengerKeys.App, MessengerKeys.Position, e.Position);

private void Paused(object sender, System.EventArgs e) =>
    MessagingCenter.Send(MessengerKeys.App, MessengerKeys.Play, false);

private void Playing(object sender, System.EventArgs e) =>
    MessagingCenter.Send(MessengerKeys.App, MessengerKeys.Play, true);

private void EndReached(object sender, System.EventArgs e) =>
    MessagingCenter.Send(MessengerKeys.App, MessengerKeys.EndReached);

private void LengthChanged(object sender, MediaPlayerLengthChangedEventArgs e) =>
    MessagingCenter.Send(MessengerKeys.App, MessengerKeys.Length, e.Length);

private void TimeChanged(object sender, MediaPlayerTimeChangedEventArgs e) =>
    MessagingCenter.Send(MessengerKeys.App, MessengerKeys.Time, e.Time);
~~~~

We _could_ also use the reverse event stream to control playback (one of several ways to do so).

~~~~csharp
const long OFFSET = 5000;

// subscribe to UI app events for seeking.
MessagingCenter.Subscribe<string>(MessengerKeys.App, 
    MessengerKeys.Rewind, vm => _mp.Time -= OFFSET);

MessagingCenter.Subscribe<string>(MessengerKeys.App, 
    MessengerKeys.Forward, vm => _mp.Time += OFFSET);
~~~~

Now that our playback service is up and running, we need to use it from the ViewModel (which is bound to the UI). This is one way of doing so:

~~~~csharp
public override Task LoadAsync()
{
    _playbackService.Init();

    MessagingCenter.Subscribe<string, float>(MessengerKeys.App, 
        MessengerKeys.Position, (app, position) => Progress = position);

    MessagingCenter.Subscribe<string, long>(MessengerKeys.App, 
        MessengerKeys.Time, (app, time) =>
        {
            RemainTime = TimeSpan.FromMilliseconds((double)new decimal(_length - time));
            StartTime = TimeSpan.FromMilliseconds((double)new decimal(time));
        });

    MessagingCenter.Subscribe<string, long>(MessengerKeys.App, 
        MessengerKeys.Length, (app, length) => _length = length);

    MessagingCenter.Subscribe<string>(MessengerKeys.App, 
        MessengerKeys.EndReached, app => EndReached());

    LoadSong();

    _playbackService.Play(true);

    return base.LoadAsync();
}
~~~~

Updating ViewModel properties such as `Progress`, `RemainTime` and `StartTime` will automatically refresh the UI and trigger Skia animations accordingly.

`Play` and `Pause`, as well as seeking, has also been bound from the UI to the libvlc engine using `LibVLCSharp`.

<p align="center">
    <img src="/assets/pulse-music-playback.gif" />
</p>

#### Credits:

- [Anish Chandran](https://dribbble.com/anish_chandran) for the PulseMusic design concept.
- [Javier Suárez Ruiz](https://twitter.com/jsuarezruiz) for the PulseMusic Xamarin.Forms [UI implementation](https://github.com/jsuarezruiz/PulseMusic).

###### _Note: I deliberately picked a Creative Commons licensed version of the song "Imagine Dragons - Radioactive" (remix). See [https://archive.org/details/ImagineDragons_201410](https://archive.org/details/ImagineDragons_201410) and its [license](https://creativecommons.org/licenses/by-nd/3.0/) for more information_.

This sample is available on the [VideoLAN GitLab](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/PulseMusic).