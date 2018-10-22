---
layout: post
title:  "How to record an HLS stream with LibVLCSharp and .NET Core"
date:   2018-10-10 10:10:40 +0700
categories: hls
---
# How to record an HLS stream with LibVLCSharp and .NET Core

In this post, I will show you how to record an HTTP Live Streaming (HLS) stream using LibVLCSharp in a .NET Core CLI app on Windows.

HLS is a streaming protocol originally developped by Apple which is supported in most internet browser and streaming media servers. [Wikipedia](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) has some info about it:

> HLS resembles MPEG-DASH in that it works by breaking the overall stream into a sequence of small HTTP-based file downloads, each download loading one short chunk of an overall potentially unbounded transport stream. A list of available streams, encoded at different bit rates, is sent to the client using an extended M3U playlist.

So, to get started we need to pull 2 packages from the VideoLAN [NuGet](https://www.nuget.org/profiles/videolan)

~~~~xml
<Project Sdk="Microsoft.NET.Sdk">  
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <RestoreProjectStyle>PackageReference</RestoreProjectStyle>
    <CopyLocalLockFileAssemblies>true</CopyLocalLockFileAssemblies>
  </PropertyGroup>  
  <ItemGroup>
    <PackageReference Include="VideoLAN.LibVLC.Windows" Version="3.0.4" />
    <PackageReference Include="LibVLCSharp" Version="0.0.3" />
   </ItemGroup>
</Project>
~~~~

`VideoLAN.LibVLC.Windows` is the `libvlc` (C/C++) library compiled for the Windows platform. More info at [here](https://github.com/mfkl/libvlc-nuget).

`LibVLCSharp` are the .NET bindings that use the native `libvlc` library and expose a .NET friendly API. This [readme](https://github.com/videolan/libvlcsharp) has additional details.

Now onto the actual code

~~~csharp
static void Main(string[] args)
{
    // Record in a file "record.ts" located in the bin folder next to the app
    var currentDirectory = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location);
    var destination = Path.Combine(currentDirectory, "record.ts");

    // Load native libvlc library
    Core.Initialize();

    using (var libvlc = new LibVLC())
    using (var mediaPlayer = new MediaPlayer(libvlc))
    {
        // Redirect log output to the console
        libvlc.Log += (sender, e) 
            => Console.WriteLine($"[{e.Level}] {e.Module}:{e.Message}");

        // Create new media with HLS link
        var media = new Media(libvlc, 
                    "http://hls1.addictradio.net/addictrock_aac_hls/playlist.m3u8", 
                    Media.FromType.FromLocation);

        // Define stream output options. 
        // In this case stream to a file with the given path
        // and play locally the stream while streaming it.
        media.AddOption(":sout=#file{dst=" + destination + "}");
        media.AddOption(":sout-keep");

        // Start recording
        mediaPlayer.Play(media);

        Console.WriteLine($"Recording in {destination}");
        Console.WriteLine("Press any key to exit");
        Console.ReadKey();
    }
}
~~~~

1. First we load the `libvlc` dlls (included in the `VideoLAN.LibVLC.Windows` nuget package).
2. Then we create 3 LibVLCSharp objects: a `LibVLC`, a `MediaPlayer` and a `Media` (containing the URL of the HLS stream).
3. To get a better understanding of what is going on, we hook up the `libvlc` log output with the console.
4. We add `libvlc` CLI options to the media, such as an output path destination ([more info](https://wiki.videolan.org/VLC_command-line_help/))
5. At last, we start the process by calling `Play` on the `MediaPlayer`. 
6. After a while, you will find a `record.ts` file located at `RecordHLS\bin\Debug\netcoreapp2.1`.

This sample is available on the [VideoLAN GitLab](https://code.videolan.org/mfkl/libvlcsharp-samples/tree/master/RecordHLS)