---
# layout: post
title:  "Crossplatform local network browsing and media playback"
date:   2019-07-02 11:10:40 +0700
categories: libvlc crossplatform xamarin forms
classes: wide
---
One of the great features of libvlc is that it can play all sorts of network streams. This is possible thanks to a huge number of supported network protocols.

Classic network protocols such as FTP, HTTP, RTP and RTSP are well supported, but what about the usual media servers / network shares protocols?

# Browsing your own medialibrary at home

Home media servers, [NAS](https://en.wikipedia.org/wiki/Network-attached_storage) and other network shares most frequently communicate through [SMB](https://en.wikipedia.org/wiki/Server_Message_Block) or [UPnP](https://en.wikipedia.org/wiki/Universal_Plug_and_Play).

LibVLC can browse these remote file systems and play their content.

Let's see how we can achieve this in a crossplatform way using [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) and Xamarin.Forms.

# Show me the code

I used the `MasterDetail` Xamarin.Forms template to build this sample, since it has the initial navigation setup built-in.

To tell `libvlc` to start looking for local network shares, you need to create and start the [MediaDiscoverer](https://code.videolan.org/videolan/LibVLCSharp/blob/master/LibVLCSharp/Shared/MediaDiscoverer.cs) objects.

> Do not confuse it with the [RendererDiscoverer](https://code.videolan.org/videolan/LibVLCSharp/blob/master/LibVLCSharp/Shared/RendererDiscoverer.cs) type, which is used for finding remote _renderers_ such as Chromecasts.

The code would look like this:

~~~~csharp
Core.Initialize();

_libVLC = new LibVLC();

foreach (var md in _libVLC.MediaDiscoverers(MediaDiscovererCategory.Lan))
{
    var discoverer = new MediaDiscoverer(_libVLC, md.Name);
    discoverer.MediaList.ItemAdded += MediaList_ItemAdded;
    discoverer.MediaList.ItemDeleted += MediaList_ItemDeleted;
    discoverer.Start();
    _mediaDiscoverers.Add(discoverer);
}
~~~~

The `ItemAdded` event provides us with a newly found `Media` which can be a new share, or a directory/file within a share we are currently browsing. I created a simple model class for the `ListView`.

~~~~csharp
public Item(Media media)
{
    Media = media;
    Name = media.Meta(MetadataType.Title);
    IsDirectory = media.Type == MediaType.Directory;
    Type = IsDirectory ? "Directory" : "File";
}
~~~~

This is what the main `ListView` code looks like. I added databinding for a few things, such as loading new items with pull to refresh gestures and names of directories and files.

~~~~xml
<StackLayout>
    <ListView x:Name="ItemsListView"
                ItemsSource="{Binding Items}"
                VerticalOptions="FillAndExpand"
                HasUnevenRows="true"
                RefreshCommand="{Binding LoadItemsCommand}"
                IsPullToRefreshEnabled="true"
                IsRefreshing="{Binding IsBusy, Mode=OneWay}"
                CachingStrategy="RecycleElement"
                ItemSelected="OnItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="10">
                        <Label Text="{Binding Name}" 
                            LineBreakMode="NoWrap" 
                            Style="{DynamicResource ListItemTextStyle}" 
                            FontSize="16" />
                        <Label Text="{Binding Type}" 
                            LineBreakMode="NoWrap"
                            Style="{DynamicResource ListItemDetailTextStyle}"
                            FontSize="13" />
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
~~~~

For WPF, it gives this layout:

<p align="center">
    <img src="/assets/localnetwork.png" />
</p>

Just as a typical file system, each item in this list is either:

- A directory. It can be parsed and its content(s) will represent a new list of files and/or directories (and so on). It can be the root shares.
- A file. It can be parsed and played by the libvlc engine.

Once the desired media has been found, it can be played transparently by using a simple page with a `VideoView` element and setting the `Media` on the `MediaPlayer` as usual.

<p align="center">
<video width="640" height="360" controls>
  <source src="/assets/localnetwork-record.mp4" type="video/mp4">
</video>
</p>

##### Note: there is a current [limitation](https://code.videolan.org/videolan/LibVLCSharp/issues/151) with the iOS libvlc build.

As of now, the code produces native **iOS**, **Android** and **WPF** (Windows) apps with 100% shared code.

Support for **Linux** (GTK) and **macOS** (Cocoa) can be easily added without any (current code) change.

The full working sample is available on the [VideoLAN GitLab](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/LocalNetwork).
