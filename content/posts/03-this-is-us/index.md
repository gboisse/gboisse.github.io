---
slug: this-is-us
title: Making "This is Us"
date: 2025-05-13
author: Guillaume Boissé
description: On the making of a PC demo.
draft: true
---

<div style="text-align: justify">

After wrapping uo our [previous production](https://www.pouet.net/prod.php?which=94177), I started thinking about what we could do next.
In particular, I really wanted to move towards path traced lighting, the kind of which can be observed in modern RTX games such as [Cyberpunk 2077](https://www.youtube.com/watch?v=Tk7Zbzd-6fs).
A key challenge for this one is that since our demoengine is still using OpenGL, meaning we can't rely on hardware-accelerated ray tracing, but more on that later :slightly_smiling_face:
<!--therefore all ray tracing has to happen in "software", that is to say, a compute shader, rather then hardware acceleration, as there's no RT extension as of yet.-->

<!--as this has always been a fascination of mine.-->
furthermore, our previous production was fairly static, moving from one mostly non-moving scene to the next, so i wanted to give this one some more motion and more abstract effects.

all in all, i set up on the following "effects" that i wanted our production to showcase, hopefully wrapped up in some story of sort:
- path tracing
- volumetric shadowing
- fluid simulation

i'll go over these in this post and hope you can take something out of these experiments:)

</div>
