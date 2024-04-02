---
slug: volume-shadows
title: Volume shadows for particles
date: 2024-04-02
author: Guillaume Boissé
description: How to render real-time high quality volume shadows for particles.
draft: true
---

<div style="text-align: justify">

When working on my particle system, I really wanted to have good lighting.
There are many ways to shade particles, such as projecting an image onto them, using their distance to some other shape, etc.
But proper lighting is the one way in my opinion to get the particles to look really good.

One possible solution for this is a technique called [deep opacity maps](http://www.cemyuksel.com/research/deepopacity/), so that was my first line of attack:

<div style="text-align: center;">

<img src="/hashed-deep-opacity-map-00.jpg" width="49%" />
<img src="/hashed-deep-opacity-map-01.jpg" width="49%" /><br/>
<em>Test renders with and without hashed deep opacity maps.</em>

</div>
<br/>
There are several issues I found however with this approach;
the depth pre-pass does indeed help the generation of the initial layer but you may still end up in cases where the layer's distribution isn't a good fit to your particles distribution causing poor lighting quality.

You also have to re-generate the structure for every light source in the scene, very much like regular shadow maps, which can end up being fairly expensive depending on the scene setup.
Furthermore, how would you go on to support light sources such as area lights and skylights?
Just like shadow maps, deep opacity maps aren't really amenable to soft shadows nor illumination coming from all possible directions...

Another solution seemed required. <!-- :confused_face: -->

### Particle volumes

Thinking about this a bit more, I realised that it'd be possibly more interesting to create a single structure over the particles that I could somehow build once and re-use for every light in the scene.
Enters the "particle volume". :slightly_smiling_face:

<div style="text-align: center;">

<img src="/particle-volume-00.jpg" width="32%" />
<img src="/particle-volume-01.jpg" width="32%" />
<img src="/particle-volume-02.jpg" width="32%" /><br/>
<em>From left to right: no lighting, particle volume, shaded particles.</em>

</div>
<br/>
The idea is pretty straightforward, simply turn the particles into a 3D density field that we then use to raymarch towards the light just as you would in a regular raytracer.
The question is how to do this efficiently and entirely on the GPU...

### Building the volume

Sparse grid, etc.

### Blurring the field

blablabla

<div style="text-align: center;">

<img src="/lighting-blur-00.jpg" width="49%" />
<img src="/lighting-blur-01.jpg" width="49%" /><br/>
<em>With and without blurring the density field.</em>

</div>
<br/>
etc.

### Ray marching

blablabla.

Temporal reprojection, etc.

### Interaction with geometry

Shadow mask, patching, etc.

<div style="text-align: center;">

![hairy-teapot](/hairy-teapot.jpg)\
*Behold the hairy teapot!*

</div>

### Following a skinned mesh

blablabla.

### Conclusion

I still haven't blablabla.

</div>
