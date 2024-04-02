---
slug: road-to-revision-2023
title: Road to Revision 2023
date: 2023-10-02
author: Guillaume Boissé
description: The making of a PC demo for the Revision 2023 demoparty.
draft: true
---

<div style="text-align: justify">

Back in 2021, I got in touch with [Made](https://www.pouet.net/user.php?who=98709) in the hopes we could join our efforts in creating a PC demo for [Revision](https://2023.revision-party.net/).
Made has been making demos for years and had recently won two Revision parties in that category, an impressive feat.
So I sent him a couple of renders I made with my personal engine hoping that'd get him excited enough...

<!--I had started turning my personal graphics engine into a "demoengine" about two years earlier with the aim of releasing demoscene productions.-->

<!--
Got in touch with Made who had won the last two revisions in hope that...\
Positive reaction :slightly_smiling_face: and he soon proposed some concept idea.
-->

He accepted pretty much instantly, so we started talking about various creative ideas and quickly settled on a concept that he had on the backburner.
Pretty shortly afterwards, we had a nice storyboard. :slightly_smiling_face:

<!--He soon came up with some concept ideas and after some initial discussions, we settled on one.
We soon had a storyboard ready...-->

<div style="text-align: center;">

![reality-check-storyboard](/reality-check-storyboard.png)
*Final storyboard for Reality Check.*

</div>

Pretty soon afterwards, we got joined by Miguel, a.k.a., [BevelledApe](https://www.pouet.net/user.php?who=106515), who was willing to handle the modelling and texturing side of things (and there was lots to do on that front!).

From this point on, everyone started working on advancing bits and pieces on the designing, modelling and programming sides of things but, without proper focus and collaboration, not much was really happening.

Towards the end of 2022, we arrived at a point where it seemed like most of the engine features that we'd need were implemented and working.
On the visual side however, besides some nice Blender renders, we still didn't have much to show for...

<!--It became clear blablabla, so in March 2023 we organised a trip to Bilbao, got a lot done, and got a good working/collaboration rythm.-->

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

<!--After the high of completing the opening sequence, things started looking pretty grim.-->

At this point, we thankfully started getting the help of Naël, a friend of Made, who was both able and willing to spend some time fixing the city scene.
This brought us to a fully working city made of ~300k triangles and correct looking materials.
Thanks Naël! <!--:slightly_smiling_face:-->

<div style="text-align: center;">

![city-shot](/city-shot.jpg)
*City scene finally imported and rendered correctly.*

</div>

<!--
Particularly the city shots turned out to be troublesome.
Plugin blablabla
-->

Things were starting to look pretty good and Made and myself could finish up the final bits of color grading for the opening sequence working together in some cafe on the last day of our Bilbao stay.
With this, we had at least fully completed the first third of our production...

... until I realised we were running into serious performance issues.
The framerate would start stuttering strongly and erratically throughout the sequence.
We initially put that onto my laptop's GPU throttling under the load (and it was getting pretty hot indeed).
But the same issue was still showing on some powerful desktop PC back at home... crap. :confused:

<!--
Frame could start stuttering strongly and erratically throughout the first sequence.\
Put that onto my laptop's GPU throttling under the load...\
Same issue after trying out on a workstation... crap:/\
-->

I took this as an opportunity to add some more statistics and metrics to the engine for both resource usage and memory amounts, which helped in identifying the culprit for the stuttering.
Indeed, I realised that our shadow [FBOs](https://en.wikipedia.org/wiki/Framebuffer_object), used for rendering the [shadow maps](https://en.wikipedia.org/wiki/Shadow_mapping), were being re-created every time a light would turn on or off.

This was initially designed as an optimisation, where a light source with null emission wouldn't draw shadow maps, since it wouldn't contribute to the image anyway.
But we had flickering lights in our content, which in turn was triggering thousands of FBO destruction and creation events resulting in the heavy stuttering we had observed.
Thankfully, once located, the error was trivial to fix and I was now able to witness a solid 99+% GPU usage all throughout the opening shot. :slightly_smiling_face:

<!--
Found out shadow FBOs were being re-created everytime a light would turn on/off due to flickering!!\
Fixed it :slightly_smiling_face:
-->

<div style="text-align: center;">

![reality-check-gpu-utilisation](/reality-check-gpu-utilisation.jpg)
*No more stuttering, smooth sailing from now on?*

</div>

Things were looking good!\
Until we started putting together the final package...

The demo was about 3GiB in size and was taking over 3 minutes to load.
Furthermore, the VRAM usage was crazy high, requiring a total of more than 12GiB, making it impossible to run on my laptop's aging GTX 1060.
I had assumed we weren't really going to need any form of texture compression, but it was becoming clear that this assumption was in fact incorrect.

<!--
About 3GiB in size, over 3 mins of loading time and using over 12GiB of RAM (couldn't run on my laptop anymore...).\
Clearly, loading all textures with no kind of compression wasn't going to cut it...
-->

So I started adding [block compression](https://en.wikipedia.org/wiki/S3_Texture_Compression) support in anger with less than a week to go before Revision starts.
Furthermore, I deferred the loading of the textures until after all assets had been processed;
this allowed going wide across every single image inside the demo for full multi-threaded texture loading, which helped bring the loading time down to about a minute.

Finally, we performed lots of asset cleaning directly at the party place, sizing down textures and compressing them to jpegs, which left us with a package size of ~800MiB while our VRAM usage went down to ~4GiB.
It could be improved further for sure, but at this point, we were well out of time...

<!--
So I added in a rush DXT1 and DXT5 support, deferred the texture loading until all assets had been processed allowing to go wide project-wise for fully multi-threading loading.\
Our RAM usage went down to ~4GiB (!) and loading time was just over a min. on my laptop.\
Finally, lots of asset cleaning, sizing down textures and compressing them to jpegs left us with a package size of ~800MiB.\
Could be better, but at this point we were well out of time.
-->

<!--Shader preloading, etc.-->

Meanwhile, Made was finishing work with [med](https://www.pouet.net/user.php?who=288) on completing the music, so we could start syncing and locking the scenes timings.
That's when we realised that the pacing wasn't great during the second part of the demo.
After aligning the content to the song structure, the transitions between shots felt too long...
So we went ahead and decided to add a brand new scene to fix things. :slightly_smiling_face:

<!--
, trying to match the music -> transitions were too long...
So we went ahead and decided to add a brand new scene :slightly_smiling_face:
-->

<div style="text-align: center;">

![last-minute-scene](/last-minute-scene.jpg)
*Impromptu scene put together right before the deadline.*

</div>

\
At this point, the deadline was only hours away, so we decided to stop tweaking and hand in the demo as it was.
We uploaded it to a USB stick and brought that over to the organisers so we could test on the compo maching and make sure everything was working fine.

That's when we found out that the demo was simply failing to launch...

<div style="text-align: center;">

![reality-check-error](/reality-check-error.jpg)
*The demo wouldn't even launch on the compo machine...*

</div>

I'd be lying if I'd say I didn't feel a little bit of stress at that moment.
It started daunting on me that without a fix for this, we may as well have no production at all...

My first immediate thought was to blame it on some Windows 11 issue, but nope, the organisers were indeed using a Windows 10 machine, crap.
The message also wasn't indicative of any missing dll and our various google searching efforts were not returning any particularly useful answer...

<!--
I first I thought it'd be caused by using Windows 11, but no, this was a Windows 10 machine, crap...\
No dll missing, etc.

After some research, no fix seemed obvious, we had just gone from being done to having no demo at all!
-->

At this point, I got some sort of inspiration;
I decided to try and rebuild the demo using the latest version of Visual Studio (i.e., 2022 instead of 2017, which I tend to stick with)... and things magically worked!
Phew...
<!--the magic happened, the demo loaded fine and played all the way through with no issue!-->

The demo compo itself was very long with the total number of entries spoiled beforehand due to some technical blunder.
So we went for some rest at the hotel and came back just in time for the start of the PC compo.
There were lots of entries but we ended up being ranked fairly late and amongst the best productions. :slightly_smiling_face:

<div style="text-align: center;">

![reality-check-entry](/reality-check-entry.jpg)
*Positioned 36th out of 41 entries, not bad!*

</div>

As the demo loaded and started playing, the audio sounded pretty broken and glitchy, crap, not much to be done now...
Thankfully, the issue cleared out after a few seconds and no further problem occurred, phew.

And we ended up 4th, just short of the podium...

</div>
