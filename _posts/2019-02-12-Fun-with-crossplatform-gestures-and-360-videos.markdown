---
# layout: post
title:  "Fun with crossplatform gestures and 360 videos"
date:   2019-02-12 11:10:40 +0700
categories: libvlc 360 xamarin forms ux
classes: wide
---
Touch management is handled quite differently on Android and iOS. Xamarin.Forms offers a nicely designed abstraction, inspired from the iOS APIs, which works across Android, iOS and UWP.

#### Seek and volume

Gestures can be useful to provide effective UX for seeking on videos and changing volume. Let's see how we can implement exactly that.

We need a view that receives touch and we will display information on which actions it takes depending on the gestures.

~~~~xml
<ContentPage.Content>
    <Grid>
        <Grid.GestureRecognizers>
            <PanGestureRecognizer PanUpdated="PanUpdated"/>
        </Grid.GestureRecognizers>
        <AbsoluteLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
            <vlc:VideoView x:Name="videoView" MediaPlayer="{Binding MediaPlayer}" 
                            AbsoluteLayout.LayoutBounds="0,0,1,1" 
                            AbsoluteLayout.LayoutFlags="All" 
                            InputTransparent="True"/>
            <Label Text="{Binding Message}" 
                    AbsoluteLayout.LayoutBounds=".5,1,1,.1" 
                    AbsoluteLayout.LayoutFlags="All" 
                    FontSize="20" 
                    LineBreakMode="WordWrap" 
                    TextColor="White" />
        </AbsoluteLayout>
    </Grid>
</ContentPage.Content>
~~~~

The [PanGestureRecognizer](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/gestures/pan) is what we need.

> The pan gesture is used for detecting the movement of fingers around the screen and applying that movement to content.

Additionally, I've put the usual `VideoView` in a crossplatform `AbsoluteLayout` to be able to position text on it as I want. That's what the `Label` is for.

On the C# side, the interesting bit is how the `PanUpdated` event is handled.

~~~~csharp
internal void OnGesture(PanUpdatedEventArgs e)
{
    switch (e.StatusType)
    {
        case GestureStatus.Running:
            if (e.TotalX < 0 && Math.Abs(e.TotalX) > Math.Abs(e.TotalY))
            {
                var timeDiff = Convert.ToInt64(e.TotalX * 1000);
                _finalTime = MediaPlayer.Time + timeDiff;

                if (WillOverflow)
                    break;

                Message = FormatSeekingMessage(timeDiff, _finalTime, Direction.Left);
                _timeChanged = true;
            }
            else if (e.TotalX > 0 && Math.Abs(e.TotalX) > Math.Abs(e.TotalY))
            {
                var timeDiff = Convert.ToInt64(e.TotalX * 1000);
                _finalTime = MediaPlayer.Time + timeDiff;

                if (WillOverflow)
                    break;

                Message = FormatSeekingMessage(timeDiff, _finalTime, Direction.Right);
                _timeChanged = true;
            }
            else if (e.TotalY < 0 && Math.Abs(e.TotalY) > Math.Abs(e.TotalX))
            {
                var volume = (int)(MediaPlayer.Volume + e.TotalY * -1);
                _finalVolume = VolumeRangeCheck(volume);

                Message = FormatVolumeMessage(_finalVolume, Direction.Top);
                _volumeChanged = true;
            }
            else if (e.TotalY > 0 && e.TotalY > Math.Abs(e.TotalX))
            {
                var volume = (int)(MediaPlayer.Volume + e.TotalY * -1);
                _finalVolume = VolumeRangeCheck(volume);

                Message = FormatVolumeMessage(_finalVolume, Direction.Bottom);
                _volumeChanged = true;
            }
            break;
        case GestureStatus.Started:
        case GestureStatus.Canceled:
            Message = string.Empty;
            break;
        case GestureStatus.Completed:
            if (_timeChanged)
                MediaPlayer.Time = _finalTime;
            if (_volumeChanged && MediaPlayer.Volume != _finalVolume)
                MediaPlayer.Volume = _finalVolume;

            Message = string.Empty;
            _timeChanged = false;
            _volumeChanged = false;
            break;
    }
}
~~~~

- The gesture has 4 states, we mostly do something during the _Running_ and _Completed_ ones.
- First we determine if the user is performing an horizontal swipe or a vertical one. The horizontal swipe will change the video position while the vertical one will be used to change the volume level.
- Either way, we compute and store a new value from the existing position/volume values and update the message on screen (`Message` is data-binded).
- When the user is lifting its finger, the event is raised with the `Completed` status and we apply the saved values to the LibVLCSharp `MediaPlayer`.

<p align="center">
    <img src="/assets/gestures.gif" />
</p>

This code works on both Android and iOS.

#### 360 videos

That very same logic can used to navigate 360 videos and change viewpoints, in a crossplatform way as well, albeit a few more considerations.

You need to check if the video being played is actually a 360 video. If it is, then the seeking/volume gestures should be disabled.

~~~~csharp
bool Is360Video => _media.Tracks[0].Data.Video.Projection == VideoProjection.Equirectangular;
~~~~

You need to be aware of the screen dimensions for 360 navigation calculations.

~~~~csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height);

    ScreenWidth = width;
    ScreenHeight = height;
}
~~~~

Now, you are ready to handle the touch events. Here is a basic example code.

~~~~csharp
void PanUpdated(object sender, PanUpdatedEventArgs e)
{
    switch (e.StatusType)
    {
        case GestureStatus.Running:
            if (ScreenWidth > 0 && ScreenHeight > 0)
            {
                double range = Math.Max(ScreenWidth, ScreenHeight);
                float yaw = (float)(Fov * -e.TotalX / range);// up/down
                float pitch = (float)(Fov * -e.TotalY / range);// left/right
                MediaPlayer.UpdateViewpoint(Yaw + yaw, Pitch + pitch, Roll, Fov);
            }
            break;
        case GestureStatus.Started:
        case GestureStatus.Canceled:
            break;
        case GestureStatus.Completed:
            Fov = MediaPlayer.Viewpoint.Fov;
            Pitch = MediaPlayer.Viewpoint.Pitch;
            Roll = MediaPlayer.Viewpoint.Roll;
            Yaw = MediaPlayer.Viewpoint.Yaw;
            break;
    }
}
~~~~

Below is a demonstration of the 360 capability in the VLC Android application. Note that the sample code linked below also includes a screen with 360 gestures support with LibVLCSharp.

<p align="center">
<iframe src="https://player.vimeo.com/video/254723180" width="640" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<p><a href="https://vimeo.com/254723180">VLC 3.0 playing 8K 48fps 360 video on Android Galaxy S8</a> from <a href="https://vimeo.com/videolan">VideoLAN</a> on <a href="https://vimeo.com">Vimeo</a>.</p>
</p>
This sample is available on the [VideoLAN GitLab](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/Gestures/Gestures).