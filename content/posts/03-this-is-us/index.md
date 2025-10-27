---
slug: this-is-us
title: Making "This is Us"
date: 2025-10-27
author: Guillaume Boissé
description: On the making of a PC demo.
draft: true
---

<div style="text-align: justify">

About 6 months ago, we released a PC production at [Revision 2025](https://www.pouet.net/party.php?which=1550&when=2025).
If you haven't seen it yet, go and watch it [there](https://www.youtube.com/watch?v=W7Om9rf0qKc). :slightly_smiling_face:

<div style="text-align: center;">

![this-is-us](/this-is-us.jpg)\
*Shot from the intro sequence of [This is Us](https://www.pouet.net/prod.php?which=103999).*

</div>

In this post, I thought I'd go through some of the graphics techniques that were showcased in the demo and that I think are exciting.

### Path tracing

Arguably, this is the biggest one. :slightly_smiling_face:

One thing to keep in mind here though is that the engine I'm using for these demos is still based on OpenGL, which means that hardware-accelerated ray tracing isn't an option...
Not a big issue, as the engine supports since its inception (circa 2015-16) an acceleration structure for ray/triangle intersection loosely based on this [reference](https://directtovideo.wordpress.com/2013/05/08/real-time-ray-tracing-part-2/) initially, which I later extended to [irregular grids](https://graphics.cg.uni-saarland.de/papers/perard-grids-preprint.pdf).

So, this calls for a somewhat different thinking from the [current](https://www.youtube.com/watch?v=Tk7Zbzd-6fs) [path tracing](https://www.youtube.com/watch?v=waizZ-UZr7U) [developments](https://www.youtube.com/watch?v=xHQMehFJ0AY) that can be seen in games for instance.
Specifically, the ray count really should be kept as low as possible so the framerate remains at 60Hz...

<div style="text-align: center;">

![pt-shot](/pt-shot.jpg)
*The path tracer in action inside the timeless "Sponza" scene.*

</div>

... which is a bit of a challenge when tackling path tracing.
In short, path tracing requires you to perform a random choice every time you hit a surface, explore the direction resulting from that random choice (using ray tracing), only to then repeat these same steps on the next hit!
Not only does this typically equate to lots of expensive rays being traced, the end result is also generally unusably noisy. :confused:

Enters radiance caching.

The idea behind radiance caching is to terminate the paths early into a data structure storing an approximation of the scene's lighting.
Not only is the performance improved due to the traces being shallower, the noise is also greatly reduced thanks to the filtering offered by the data structure.

<div style="text-align: center;">

<img src="/pt-00.jpg" width="49%" />
<img src="/pt-01.jpg" width="49%" /><br/>
<em>Test renders with and without radiance caching.</em>

</div>
<br/>
blablabla

### Fluid simulation

This is an area I've been wanting to explore further for quite some time.
Still a lot more to explore, but I'm quite happy with what we've been able to show... the current state of it...

### Miscellaneous

blablabla

</div>
