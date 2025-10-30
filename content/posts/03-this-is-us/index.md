---
slug: this-is-us
title: Making "This is Us"
date: 2025-10-27
author: Guillaume Boissé
description: Behind the scenes of a modern PC demo.
draft: true
---

<div style="text-align: justify">

About 6 months ago, we presented a PC demo production at [Revision 2025](https://www.pouet.net/party.php?which=1550&when=2025).
If you haven't seen it yet, go watch it [here](https://www.youtube.com/watch?v=W7Om9rf0qKc). :slightly_smiling_face:

<div style="text-align: center;">

![this-is-us](/this-is-us.jpg)\
*Shot from the intro sequence of [This is Us](https://www.pouet.net/prod.php?which=103999).*

</div>

In this post, I thought I'd go through some of the graphics techniques that were showcased in the release and that I think are exciting.

### Path tracing

Arguably, this is the biggest one. :slightly_smiling_face:

One thing to keep in mind though is that the engine used here is still based on OpenGL, which means that hardware-accelerated ray tracing isn't an option... (at least not until someone makes an extension for it, akin to the recently merged mesh shaders [extension](https://www.phoronix.com/news/OpenGL-Mesh-Shader-Added))
Not a big issue, as the engine supports since its inception (circa 2015-16) an acceleration structure for ray/triangle intersection loosely based on this [reference](https://directtovideo.wordpress.com/2013/05/08/real-time-ray-tracing-part-2/) initially, although later extended to [irregular grids](https://graphics.cg.uni-saarland.de/papers/perard-grids-preprint.pdf).

So, this calls for a somewhat different thinking from the [current](https://www.youtube.com/watch?v=Tk7Zbzd-6fs) [path tracing](https://www.youtube.com/watch?v=waizZ-UZr7U) [developments](https://www.youtube.com/watch?v=xHQMehFJ0AY) that can be seen in games for instance.
Specifically, the ray count really should be kept as low as possible, so that the framerate can remain at 60Hz...

<div style="text-align: center;">

![pt-shot](/pt-shot.jpg)
*The path tracer in action inside the timeless "Sponza" scene.*

</div>

... which is a bit of a challenge when tackling path tracing.
In short, path tracing requires you to perform a random choice every time you hit a surface, explore the direction resulting from that random choice (using ray tracing), only to then repeat these same steps on the next hit.
Not only does this typically equate to lots of expensive rays being cast, but the end result is also generally unusably noisy. :confused:

Enters radiance caching.

The idea behind radiance caching is to terminate the paths early into a data structure that approximates the scene's lighting.
Not only is the performance improved due to the traces being shallower, the noise is also greatly reduced thanks to the filtering offered by the data structure.

<div style="text-align: center;">

<img src="/pt-00.jpg" width="49%" />
<img src="/pt-01.jpg" width="49%" /><br/>
<em>Test renders without and with radiance caching.</em>

</div>
<br/>
Here, we'll be using <a href="https://arxiv.org/abs/1902.05942">spatial hashing</a> to generate the structure and the update is as follows:

1. Once a frame, go through all the cells (initially, there are none) and check whether the decay has completed; evict as required.
1. Every time the cache is looked up, do the following:
   1. Hash the position and normal at the hit point to build a list of affected cells (these may be new cells). Make sure to reset the decay back to its original value. (If the cell was already estimated this frame, we can exit here.)
   1. Pick a hit point at random for every cell in the list; we'll use it for computing the direct lighting contribution for the cell as well as for spawning a "bounce ray" (using cosine-weighted sampling for instance).
   1. Whatever the bounce ray hits, check whether a cell exists; if so, use its radiance as contribution, if not, do nothing.

A great property of this approach is that we only trace one "bounce ray" per affected cell, while still getting a fairly decent approximation of "infinite" multiple bounces (temporally recurrent, in fact).
This turns out to be a great knob for balancing quality vs. performance.
Indeed, using smaller cells, we achieve greater fidelity but at higher cost.
Conversely with larger cells, we introduce more bias but our path tracer now runs much faster.
This property can easily be tweaked on a per-scene basis to obtain the best possible results. :slightly_smiling_face:

Still, the image remains fairly noisy.

The second technique we'll employ here is known as [ReSTIR](https://intro-to-restir.cwyman.org/) (short for "Resampled Spatio-Temporal Importance Resampling").
And specifically, the "GI" [variant](https://research.nvidia.com/publication/2021-06_restir-gi-path-resampling-real-time-path-tracing) of it.

<div style="text-align: center;">

<img src="/pt-01.jpg" width="49%" />
<img src="/pt-02.jpg" width="49%" /><br/>
<em>Test renders without and with ReSTIR.</em>

</div>
<br/>
Covering the depths and details of ReSTIR would make for a post of its own, so suffice to say that the approach chosen here, unsurprisingly, aims at minimizing the number of rays being cast.

A cool trick for this was proposed by the folks over at [Traverse Research](https://blog.traverseresearch.nl/dynamic-diffuse-global-illumination-b56dc0525a0a).
They pointed out that one could simply retrieve an AO mask (short for "Ambient Occlusion") from the initial ray trace.
This mask wouldn't be applied to the lighting directly, as is usually the case with AO, but rather used for weighting the validity of combining reservoirs.
If the reservoirs' AO values match closely, the probability of combining them increases, otherwise, it is reduced.
This greatly improves preservation of contact shadows and details, at no additional ray tracing cost. :slightly_smiling_face:

Finally, the image is cleaned up using some standard spatiotemporal denoising.
The denoiser itself is fairly simple (and fast); use temporal reprojection when possible, fall back to spatial filtering when not.
The number of accumulated temporal samples is stored in the alpha channel and is used to "fade away" the amount of spatial filtering as history becomes more reliable.

<div style="text-align: center;">

<img src="/pt-03.jpg" width="49%" />
<img src="/pt-04.jpg" width="49%" /><br/>
<em>Denoised diffuse and specular signals.</em>

</div>
<br/>
A quick word on specular; it uses about the same data flow than diffuse but with a few additional dedicated heuristics.
Specifically, "BRDF-based ratio estimator" by <a href="https://www.youtube.com/watch?v=YwV4GOBdFXo">Tomasz Stachowiak</a> is used for upscaling the signal (the path tracer typically renders at ¼ spp, or even ⅛ spp at times...), while "dual-source reprojection" (by the same author) ensures that smooth surfaces are reprojected (more or less) correctly.

<div style="text-align: center;">

![pt-shot](/pt-05.jpg)
*The composited image... finally!*

</div>

### Fluid simulation

This is an area I've been wanting to dive further into for quite some time.
While there's still a lot more to explore for me (hopefully in some not-so-distant future production...), I'm quite happy with what we've been able to show back in April.

The setup I ended up with was mostly inspired by this post from [Morten Vassvik](https://bsky.app/profile/vassvik.bsky.social/post/3lb6j2wnmtk2k).
The idea is to pre-fetch the neighboring information efficiently into LDS (short for "Local Data Share") by having all lanes cooperate to the operation.
We can then synchronize the group and go at performing our computations with all the neighboring cells' information close by and ready for fast access. :slightly_smiling_face:

In my scenario however, I was interested in dealing with particles and implementing [smoothed-particles hydrodynamics](https://en.wikipedia.org/wiki/Smoothed-particle_hydrodynamics), or SPH for short.

<div style="text-align: center;">

![tiled-sph](/tiled-sph.gif)
*Some early test of the tiled SPH approach.*

</div>

blablabla

Spatial hashing again!

blablabla

<div style="text-align: center;">

![sph-bunny](/sph-bunny.jpg)
*Some Marching cube'd fluid thrown onto the Stanford bunny.*

</div>

Dilate the tiles...

blablabla

### Miscellaneous

This post is pretty long already but I thought I'd still briefly mention how the lighting (and shadows specifically) are computed for particles.

This time, we'll want to traverse the grid many times and in many different directions (a technique known as "ray marching").
Spatial hashing isn't a good fit here, as the overhead of accessing each subsequent cell would simply kill the performance.
<!--So, another approach is required.-->

blablabla

</div>
