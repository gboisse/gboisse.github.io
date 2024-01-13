---
slug: road-to-revision-2023
title: Road to Revision 2023
date: 2023-10-02
author: Guillaume Boissé
description: Preparing a PC demo for the Revision 2023 demoparty.
draft: true
---

<div style="text-align: justify">

Back in 2021, I got in touch with [Made](https://www.pouet.net/user.php?who=98709) in the hopes we could join our efforts in creating a PC demo for Revision.
Made had been making demo for years and had recently won the past two Revision parties in that category, an impressive feat.
So I sent him a couple of screenshots from my engine and its node editor hoping that'd get him excited and blablabla.

<!--
Got in touch with Made who had won the last two revisions in hope that...\
Positive reaction :slightly_smiling_face: and he soon proposed some concept idea.
-->

He soon came up with some concept ideas and after some initial discussions, we settled on one.
We soon had a storyboard ready...

<div style="text-align: center;">

![reality-check-storyboard](/reality-check-storyboard.png)
*Early storyboard for Reality Check.*

</div>

Pretty soon afterwards, we got joined by Miguel (a.k.a., [bevelledape](https://www.pouet.net/user.php?who=106515)) who would handle the modelling and texturing side of things (and there was lots to do on that front!).

From this point, not really much happened; everyone was working a bit on advancing things but without proper focus and collaboration, not much was happening.

It became clear blablabla, so in March 2023 we organised a trip to Bilbao, got a lot done, and got a good working/collaboration rythm.

<div style="text-align: center;">

![reality-check-miguel](/reality-check-miguel.jpg)
*Miguel in full demo mode at the Bilbao flat* :slightly_smiling_face:

</div>

...

Frame could start stuttering strongly and erratically throughout the first sequence.\
Put that onto my laptop's GPU throttling under the load...\
Same issue after trying out on a workstation... crap:/\

Found out shadow FBOs were being re-created everytime a light would turn on/off due to flickering!!\
Fixed it :slightly_smiling_face:

<div style="text-align: center;">

![reality-check-gpu-utilisation](/reality-check-gpu-utilisation.jpg)
*No more stuttering, smooth sailing from now on?*

</div>

Things were looking good!

Until we started putting together the final package...\
About 3GiB in size, over 3 mins of loading time and using over 12GiB of RAM (couldn't run on my laptop anymore...).\
Clearly, loading all textures with no kind of compression wasn't going to cut it...

So I added in a rush DXT1 and DXT5 support, deferred the texture loading until all assets had been processed allowing to go wide project-wise for fully multi-threading loading.\
Our RAM usage went down to ~4GiB (!) and loading time was just over a min. on my laptop.\
Finally, lots of asset cleaning, sizing down textures and compressing them to jpegs left us with a package size of ~800MiB.\
Could be better, but at this point we were well out of time.

Shader preloading, etc.

Meanwhile, Made was finishing work with [med](https://www.pouet.net/user.php?who=288) on completing the music, so we could start syncing and locking scenes timings.
That's when we realised that the pace wasn't great, trying to match the music -> transitions were too long...
So we went ahead and decided to add a brand new scene :slightly_smiling_face:

<div style="text-align: center;">

![last-minute-scene](/last-minute-scene.jpg)
*Impromptu scene created right before the deadline.*

</div>

\
At this point, the deadline was approaching fast, so we decided to stop tweaking and hand in the demo as it was.
Put it on a USB stick, brought it over to the organisers to test everything was working fine and the demo would fail to launch...

<div style="text-align: center;">

![reality-check-error](/reality-check-error.jpg)
*The demo wouldn't launch on the compo PC only a few hours before the deadline...*

</div>

I first I thought it'd be caused by using Windows 11, but no, this was a Windows 10 machine, crap...\
No dll missing, etc.

After some research, no fix seemed obvious, we had just gone from being done to having no demo at all!

At this point, I got some sort of inspiration;
I decided to try and rebuild the demo using the latest version of Visual Studio (i.e., 2022 instead of 2017, which I tend to stick with)... and it worked!
Phew...
<!--the magic happened, the demo loaded fine and played all the way through with no issue!-->

Demo compo was very long, went for some rest and got back just in time for the start of the PC compo!\
Lots of entries and we were actually ranked amongst the last productions :slightly_smiling_face:

<div style="text-align: center;">

![reality-check-entry](/reality-check-entry.jpg)
*Number 36 out of 41 entries, not bad* :slightly_smiling_face:

</div>

As the demo loaded and started to play, the audio was pretty broken and glitchy, crap, no much to be done now...\
Thankfully, this cleared out after a few seconds and no further issue occurred, phew.

Ended up 4th, just short of the podium...

Until next time!

</div>
