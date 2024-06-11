---
slug: renderer-design
title: Designing a flexible renderer
date: 2024-06-09
author: Guillaume Boissé
description: Case study of a flexible renderer design.
draft: true
---

<div style="text-align: justify">

In this post, I'd like to describe the architecture of my personal graphics engine called "RogueEngine".
Most of the choices mentioned here date back from some time around 2016/17, so not exactly new...
Nevertheless, I think some of the design decisions remain interesting even today.

So, back in 2016, I decided to refactore/re-write the engine, one of the main goals being to follow the ["AZDO"](https://www.gdcvault.com/play/1020791/Approaching-Zero-Driver-Overhead-in) guidelines.
AZDO, or Approaching Zero Driver Overhead, is fondamentally about batching draw calls as much as you can to avoid calling OpenGL API functions too much therefore reducing driver involvement, mainly in terms of frame performance.
It also tends to set things up in a way that are well suited for ray tracing (random access to all geometry, all materials, all textures, etc.), which was at the time (and still is today to a large extent) a major area of focus for me.

...

Talk about forward drawing pipeline with clearly identified callbacks slots...

Renderer API is mostly "data-oriented" in the sense that all the renderer does is detect whether a particular render target was written to during these stages.
For instance, if AO is written during the pre-draw callback, then the draw shaders are re-compiled during the forward pass with `HAS_OCCLUSION` switch.

<!--
So, back in 2016, I decided to start a refactoring of the engine I had been working, which ultimately turned into a near complete re-write;
there were mostly two reasons for this:
- The previous version was aiming to be OSX-compatible and was relying on OpenGL 3.3 for graphics and OpenCL 1.1 for compute;
the interop was slow and generally I found the OSX graphics drivers to be pretty disastrous, just not worth it.
- I had watched this very inspiring talk from GDC 2014 called ["AZDO"](https://www.gdcvault.com/play/1020791/Approaching-Zero-Driver-Overhead-in) and thought this was a great opportunity, blablabla.

Command buffers, etc.
Vulkan-like performance before it existed!

Another important goal for this design was to provide a flexible rendering pipeline allowing to easily experiment/research new graphics techniques.
This was initially for enabling my personal research and experiments, and later on went on to be very useful for turning the engine into a demoengine. :slightly_smiling_face:
-->

</div>
