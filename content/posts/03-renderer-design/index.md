---
slug: renderer-design
title: Designing a flexible renderer
date: 2024-06-09
author: Guillaume Boissé
description: Case study of a flexible renderer design.
draft: true
---

<div style="text-align: justify">

In this post, I'll be describing the architecture of "RogueEngine", which is my personal graphics engine blablabla
Most of the choices described here date back from some time around 2016/17, so not exactly new...
Nevertheless, I think some design choices remain, if not relevant, then interesting at the very least, even today.

So, back in 2016, I decided to start a refactoring of the engine I had been working, which ultimately turned into a near complete re-write;
there were mostly two reasons for this:
- The previous version was aiming to be OSX-compatible and was relying on OpenGL 3.3 for graphics and OpenCL 1.1 for compute;
the interop was slow and generally I found the OSX graphics drivers to be pretty disastrous, just not worth it.
- I had watched this very inspiring talk from GDC 2014 called ["AZDO"](https://www.gdcvault.com/play/1020791/Approaching-Zero-Driver-Overhead-in) and thought this was a great opportunity, blablabla.

Command buffers, etc.
Vulkan-like performance before it existed!

Another important goal for this design was to provide a flexible rendering pipeline allowing to easily experiment/research new graphics techniques.
This was initially for enabling my personal research and experiments, and later on went on to be very useful for turning the engine into a demoengine. :slightly_smiling_face:

</div>
