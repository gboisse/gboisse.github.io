---
slug: revision-2023
title: Road to Revision 2023
date: 2024-04-08
author: Guillaume Boissé
description: The making of a PC demo for the Revision 2023 demoparty.
---

<div style="text-align: justify">

Back in 2021, I got in touch with [Made](https://www.pouet.net/user.php?who=98709) in the hope that we could join our efforts in creating a PC demo for [Revision](https://2023.revision-party.net/).
Made has been making demos for years and had recently won two Revision parties in that category, an impressive feat.
So I sent him a couple of renders I made with my personal engine hoping that'd get him excited enough...

He accepted pretty much instantly, so we started talking about various creative ideas and quickly settled on a concept that he had on the backburner.
Shortly after this, we had a nice storyboard. :slightly_smiling_face:

<div style="text-align: center;">

![reality-check-storyboard](/reality-check-storyboard.png)
*Final storyboard for "Reality Check".*

</div>

Pretty soon afterwards, we got joined by Miguel, a.k.a., [BevelledApe](https://www.pouet.net/user.php?who=106515), who was willing to handle the modelling and texturing side of things (and there was lots to do on that front!).

From this point on, everybody started working on advancing bits and pieces on the designing, modelling and programming sides of things but, without proper focus and collaboration, not much was really happening.

Towards the end of 2022, we arrived at a point where it seemed like most of the engine features that we'd need were implemented and working.
On the visual side however, besides some nice Blender renders, we still didn't have much to show for...

<div style="text-align: center;">

![reality-check-miguel](/reality-check-miguel.jpg)
*Miguel, a.k.a., [BevelledApe](https://www.pouet.net/user.php?who=106515), in full demo mode at the Bilbao flat.*

</div>

... until the start of March 2023!
It started to become clear that if we were to release anything, something had to happen.
So we organised for everyone to meet up for a full week at a flat in beautiful Bilbao.

This week turned out to be key in kicking off the production;
we made great progress on the opening sequence and got a much better flow for working all together.
We were still seriously behind on most shots however, with the deadline now less than a month away...

In particular, the city shots were turning out to be troublesome;
we had purchased some city plugin hoping it'd be enough to generate the scene.
Unfortunately the output geometry ended up being way too tessellated resulting in a total of over 23 million triangles.
This was too much especially for our lighting, which is nearly entirely ray traced using some GPU-based software ray tracer.
To make matters worse, the materials on the buildings were a complete mess, relying on many Blender-specific shading features making the scene very difficult to export in any usable way. :slightly_frowning_face:

At this point, we thankfully started getting the help of [Naël](https://demozoo.org/sceners/49100/), a friend of Made, who was both able and willing to spend some time fixing the content.
This brought us to a fully working city scene made of ~300k triangles and correct looking materials.
Thanks Naël!

<div style="text-align: center;">

![city-shot](/city-shot.jpg)
*City scene finally imported and rendered correctly.*

</div>

Things were starting to look pretty good and Made and myself could finish up the final bits of color grading for the opening sequence working together in some cafe on the last day of our Bilbao stay.
With this, we had at least fully completed the first third of our production...

... until I realised we were running into serious performance issues.
The framerate would start stuttering strongly and erratically throughout the sequence.
We initially put that onto my laptop's GPU throttling under the load (and it was getting pretty hot indeed).
But the same issue was still showing on some powerful desktop PC back at home... crap. :confused:

I took this as an opportunity to add some more statistics to the engine for both resource and memory usage, which helped in identifying the culprit for the stuttering.
Indeed, I realised that our shadow [FBOs](https://en.wikipedia.org/wiki/Framebuffer_object), used for rendering the [shadow maps](https://en.wikipedia.org/wiki/Shadow_mapping), were being re-created every time a light would turn on or off.

This was initially designed as an optimisation, where a light source with null emission wouldn't draw shadow maps, since it wouldn't contribute to the image anyway.
But we had flickering lights in our content, which in turn were triggering thousands of FBO destruction and creation events resulting in the heavy stuttering we had observed.
Thankfully, once located, the error was trivial to fix and I was now able to witness a solid 99+% GPU usage all throughout the opening shot. :slightly_smiling_face:

<div style="text-align: center;">

![reality-check-gpu-utilisation](/reality-check-gpu-utilisation.jpg)
*No more stuttering, smooth sailing from now on?*

</div>

Things were looking good!\
Until we started putting together the final package...

The demo was about 3GiB in size and was taking over 3 minutes to load.
Furthermore, the VRAM usage was crazy high, requiring a total of more than 12GiB, making it impossible to run on my laptop's aging GTX 1060.
I had assumed we weren't really going to need any form of texture compression, but it was becoming clear that this assumption was in fact incorrect.

So I started adding [block compression](https://en.wikipedia.org/wiki/S3_Texture_Compression) support in anger with less than a week to go before the start of Revision.
Furthermore, I deferred the loading of the textures until after all assets had been processed;
this allowed going wide across every single image inside the demo for full multi-threaded texture loading, which helped bring the loading time down to about a minute.

Finally, we performed lots of asset cleaning directly at the party place, sizing down textures and compressing them to jpegs, which left us with a package size of ~600MiB while our VRAM usage went down to ~3GiB.
This could be improved further for sure, but at that point, we were well out of time...

Meanwhile, Made was finishing work with [med](https://www.pouet.net/user.php?who=288) on completing the music, so we could start syncing and locking the scenes timings.
That's when we realised that the pacing wasn't great during the second part of the demo.
After aligning the content to the song's structure, the transitions between shots felt too long...
So we went ahead and decided to add a brand new scene to fix things. :slightly_smiling_face:

<div style="text-align: center;">

![last-minute-scene](/last-minute-scene.jpg)
*Impromptu scene put together right before the deadline.*

</div>

\
At this point, the deadline was only hours away, so we decided to stop tweaking and hand in the demo as it was.
We uploaded it to a USB stick and brought that over to the organisers so we could test on the compo machine and make sure everything was working fine.

That's when we found out that the demo was simply failing to launch...

<div style="text-align: center;">

![reality-check-error](/reality-check-error.jpg)
*The demo wouldn't even launch on the compo machine...*

</div>

I'd be lying if I'd say I didn't feel a little bit of stress at that moment.
It started dawning on me that without a fix for this, we may as well have no production at all...

My first immediate thought was to blame the error on some Windows 11 issue, but nope, the organisers were indeed using a Windows 10 machine, crap.
The message also wasn't indicative of any missing dll and our various google searching efforts were not returning any particularly useful answer...

At this point, I got some sort of inspiration;
I decided to try and rebuild the demo using the latest version of Visual Studio (i.e., 2022 instead of 2017, which I tend to stick with)... and things magically worked!
Phew...

The demo compo itself was very long with the total number of entries accidentally spoiled beforehand due to some technical blunder.
So we went to have some rest at the hotel and came back just in time for the start of the PC compo.
There were lots of entries but we ended up being ranked fairly late and amongst the best productions. :slightly_smiling_face:

<div style="text-align: center;">

![reality-check-entry](/reality-check-entry.jpg)
*Positioned 36th out of 41 entries, not bad!*

</div>

As the demo loaded and started playing, the audio sounded pretty broken and glitchy, crap, not much to be done now...
Thankfully, the issue cleared up after a few seconds and no further problem occurred, phew.

And we ended up 4th! Just short of the podium...

</div>
