---
title: Writing a native VLC plugin in C#
date: 2026-02-11
classes: wide
---

From a developer point of view, VLC has several integration points depending on what you are trying to achieve. They provide different levels of abstractions, capabilities and complexity.

The most common and straightforward way is the [LibVLC](https://www.videolan.org/vlc/libvlc.html) API. It is the SDK used by most applications that embed VLC, and [LibVLCSharp](https://code.videolan.org/videolan/LibVLCSharp) makes it available to .NET developers. If you need to play media in your app, this is the way to go.

Then there is the Lua scripting layer. VLC ships with Lua scripts for things like the [HTTP interface](https://code.videolan.org/videolan/vlc/-/blob/master/share/lua/intf/http.lua), playlist parsers and extensions. It is more limited than native code, but it is a dynamic scripting language that is easy to write and update. It is also, historically, the main approach for writing extensions to the main VLC desktop application.

And then there are native VLC plugins. These target `libvlccore` directly and provide the most capabilities: video filters, audio filters, demuxers, codecs, renderers, and more.

Unlike a LibVLC setup where the host app loads libvlc and calls into it, native plugins are automatically loaded and unloaded by the VLC core depending on what is needed. The core selects them based on capabilities and priorities.

These modules have traditionally been written in C or C++. More recently, there has been work on writing them [in Rust](https://code.videolan.org/videolan/vlc/-/blob/master/modules/logger/telegraf-rs/src/lib.rs) as well, though that is also relatively new and limited to a few modules so far.

This got me thinking. What about C#?

## The experiment

I had this idea over 6 years ago but never got around to it.

It is possible through the use of AOT (Ahead-Of-Time) compilation. For .NET developers not familiar with it: AOT compiles your C# code directly to native machine code at build time. No JIT, no runtime code generation, minimal runtime overhead. The CLR is still there under the hood (AOT does not remove it entirely), but what you get is a self-contained native binary with performance characteristics close to C/C++. In our case, that means a native DLL that VLC can load just like any other plugin.

I wanted to see if this was actually possible. Could we write a fully native VLC 4.x plugin in C#, have it loaded by VLC, process video frames, and render things on screen?

Turns out, yes. The result is [VLCLR](https://github.com/mfkl/vlclr), a framework for building VLC 4.x plugins in C# using Native AOT.

## What it looks like

Here is a simplified video filter plugin. The framework provides a base class and [Roslyn source generators](https://learn.microsoft.com/en-us/dotnet/csharp/roslyn-sdk/source-generators-overview) handle all the entry point boilerplate:

```csharp
[VLCModule("dotnet_overlay")]
[VLCCapability("video filter")]
[VLCDescription(".NET Native AOT Video Filter Overlay")]
[VLCConfig("dotnet-overlay-opacity", VLCConfigType.Float,
    Default = 1.0f, Min = 0.0f, Max = 1.0f,
    Description = "Overlay opacity")]
public partial class VideoOverlayFilter : VLCVideoFilterBase
{
    protected override bool OnOpen(VLCFilterContext context)
    {
        // Initialize your filter
        return true;
    }

    protected override void ProcessFrame(VLCFrame frame)
    {
        // Access frame.Pixels, frame.Width, frame.Height
        // Modify the frame data directly
    }

    protected override void OnClose()
    {
        // Clean up
    }
}
```

No C code. No interop stubs. You decorate your class with a few attributes, override the methods you need, and the Roslyn source generator produces the `vlc_entry` function, the module descriptor, the config options registration, everything VLC needs to discover and load the plugin. Minimal setup, nice developer experience.

Under the hood, the framework provides native type definitions matching VLC 4.x C structures, base classes that handle instance management and callback marshaling, and imaging utilities for frame format conversion and compositing. The source generators inspect your class attributes and generate the exact binary layout VLC expects for module registration. This catches many registration issues at compile time rather than at runtime.

The key parts of the `.csproj` are what you would expect for a Native AOT library, plus the VLC-specific bits:

```xml
<PropertyGroup>
    <PublishAot>true</PublishAot>
    <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
    <AssemblyName>libdotnet_overlay_plugin</AssemblyName>
</PropertyGroup>

<ItemGroup>
    <ProjectReference Include="..\..\src\VLCLR\VLCLR.csproj" />
    <!-- Source generator for VLC entry points -->
    <ProjectReference Include="..\..\src\VLCLR.Generators\VLCLR.Generators.csproj"
                      OutputItemType="Analyzer"
                      ReferenceOutputAssembly="false" />
</ItemGroup>

<!-- Link against libvlccore for direct P/Invoke -->
<ItemGroup>
    <DirectPInvoke Include="libvlccore" />
    <NativeLibrary Include="..\..\lib\libvlccore.lib" />
</ItemGroup>
```

The full project file is [on GitHub](https://github.com/mfkl/vlclr/blob/main/samples/VideoOverlay/VideoOverlay.csproj).

You run `dotnet publish`, drop the resulting DLL into VLC's plugin folder, and it just works. The video overlay plugin compiles down to a 4.3 MB native DLL. That includes the .NET runtime, a third-party graphics library, and an embedded font. For comparison, VLC's built-in logo plugin written in C is 30 KB. Most of the difference is the bundled .NET runtime.

## Drawing on frames with ImageSharp

For the actual rendering, drawing text and graphics onto video frames, I used the excellent [ImageSharp](https://github.com/SixLabors/ImageSharp) by [James Jackson-South](https://github.com/JimBobSquarePants). It is a fully managed 2D graphics library with no native dependencies, which makes it a perfect fit for AOT compilation.

The video overlay sample uses it to render .NET debug information directly onto each video frame, here it is in action during video playback:

<p align="center">
<video width="640" height="360" controls>
  <source src="/assets/overlay.mp4" type="video/mp4">
</video>
</p>

## Things only a native plugin can do

LibVLCSharp does let you [access raw video frames](https://code.videolan.org/videolan/LibVLCSharp/-/blob/master/samples/LibVLCSharp.CustomRendering.Direct3D11/Program.cs) from the LibVLC API for processing. But some things are only possible as a native plugin.

The repo includes a subtitle renderer sample. Subtitle rendering is not a capability exposed by the LibVLC API. You need a native plugin for this.

VLC's subtitle pipeline sends text segments to the renderer with full styling metadata: font families, sizes, colors, outline thickness, shadow offsets, and background regions. The C# renderer receives these segments, walks the styling tree, builds the layout with ImageSharp's text engine, and returns a rendered pixel region that VLC composites onto the video.

Outline rendering was one of the trickier parts: the plugin draws outlines as offset copies of the text in 8 directions, then layers the fill text on top. Getting this to look right while keeping per-frame rendering fast took some iteration.

<p align="center">
    <img src="/assets/sub-overlay1.png" />
    <img src="/assets/sub-overlay2.png" />
</p>

This is what makes native plugins interesting. VLC already has a [Whisper-based speech-to-text module](https://code.videolan.org/videolan/vlc/-/blob/f66c05cb8d346fb0881aabfe6f4596d29c11d030/modules/codec/stt_whisper.c). Imagine building something similar in C# with access to the .NET ML ecosystem (speech recognition, real-time video analysis, intelligent subtitle generation), all running inside VLC as a native plugin. These are things that are simply not reachable from the LibVLC API. If you are a .NET developer and have ever wanted to extend VLC itself rather than just embed it, this is that door.

## What was hard

The trickiest part was marshaling. VLC's internal C structures are complex: deeply nested, with unions, flexible array members, and platform-specific layout differences. Getting the C# struct definitions to match the exact binary layout VLC expects required careful work. A single field offset being wrong means silent corruption or a crash with no useful stack trace. This is the kind of interop where you spend more time reading C headers than writing C#.

The VLC 4.x plugin API is itself still evolving, which adds another layer. Some things are harder to express in managed code than others, and memory management across the native/managed boundary requires care.

## Performance

A common concern with managed code in a video pipeline is latency. In practice, the video overlay filter processes 1080p frames without visible frame drops during playback. The hot path is a memory copy from VLC's plane buffer into an ImageSharp image, the drawing operations, and a copy back, all on contiguous memory. There is no GC pressure per frame since the buffers are pre-allocated and reused. AOT-compiled code does not have JIT warmup, so the first frame is as fast as the thousandth.

## Current status

This is a proof of concept. The sample plugins work and I use them during development, but the API surface and struct definitions will evolve alongside VLC 4.x.

Currently it only runs on Windows. Linux and macOS support requires building against the platform-specific VLC SDK and testing the struct layouts. Both .NET and VLC are cross-platform, so no fundamental blockers exist. The same applies to Android and iOS.

What this demonstrates is that the .NET Native AOT toolchain is now mature enough to produce plugins for complex native applications like VLC.

## Try it yourself

```bash
git clone https://github.com/mfkl/vlclr.git
cd vlclr
dotnet publish samples/VideoOverlay -c Release -r win-x64
```

Copy the resulting DLL from `samples/VideoOverlay/bin/Release/net10.0/win-x64/native/` into your VLC 4.x `plugins/video_filter/` folder, and play a video. Note that VLC 4.x is still in development, so you will need a nightly build. See the [README](https://github.com/mfkl/vlclr) for full setup instructions including where to get the VLC 4.x nightly and SDK.

## What's next

There are more plugin types to explore (audio filters, demuxers, stream outputs), each with their own callback signatures and lifecycle. The framework currently covers video filters and subtitle renderers, but the pattern is the same: define the C# structs, write a base class, and let the source generator handle registration.

The code is on GitHub: [mfkl/vlclr](https://github.com/mfkl/vlclr). Contributions, feedback, and ideas are welcome. Open an issue or reach out on GitHub.

*I designed the architecture and APIs for this project. [Claude](https://claude.ai) wrote the implementation. Having an AI handle the code helped me finally get started on something I had been putting off for years, and move significantly faster than I would have on my own.*
