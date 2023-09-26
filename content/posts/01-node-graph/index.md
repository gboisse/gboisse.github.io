---
slug: node-graph
title: Visual node graph with ImGui
date: 2023-06-19
author: Guillaume Boissé
description: How to use Dear ImGui for creating a visual node graph.
draft: true
---

<div style="text-align: justify">

I wanted my first post on this blog to be about the node graph system that I created for my personal graphics engine named "RogueEngine".
I may talk about the engine itself in another post but for now I'd like to focus on its UI.

As a teaser, here's a screenshot of the tool running our [Revision 2023](https://www.pouet.net/party.php?which=1550&when=2023) demo:

<div style="text-align: center;">

![node-graph](/node-graph.jpg)\
*Intro sequence from [Reality Check](https://www.pouet.net/prod.php?which=94177).*

</div>

### A bit of background

I started thinking about this project back in 2019 with the aim of releasing some PC demo productions.
I had heard of the [demoscene](https://en.wikipedia.org/wiki/Demoscene) for the first time about four years earlier through some coworkers at Sony and it gradually grew on me to the point that I too wanted to participate.
I really wanted to be able to team up with artists and designers for making these demos rather than, say, other programmers or trying to complete a production on my own.
This seemed like a better way to reach a higher visual bar as well as being more entertaining overall. :slightly_smiling_face:

This translated in my mind to having an interface that anyone could use for interacting with and tweaking the content, although in retrospect, such a system comes in extremely handy for programmers too and I find myself increasingly using it for many purposes, such as research and experimentation.
Regardless, I started looking into interfaces for visual content creation, which were at the time both quite fascinating and mysterious to me having little to no experience with such systems.

I was initially not particularly fond of node graphs however, for mainly two reasons:
- Many of these systems seemed to be what I'd call "coding with nodes"; so you still get the complexity of programming only more inconvenient.
- It can get real messy real fast.

<div style="text-align: center;">

![blueprint-from-hell](/blueprint-from-hell.png)\
*Courtesy of [blueprints from hell](https://blueprintsfromhell.tumblr.com/).*

</div>

\
Node systems were still appealing to me however for different reasons;
they felt more intuitive and less intimidating than other solutions with more "traditional" UI and they tend to look really nice on screenshots.
Then maybe it'll be easier for me to convince other people to get on board? :slightly_smiling_face:

So I started thinking about a node system that would **not** be "coding with nodes".

### Node system

Having little to no experience with node-based systems, I went ahead and looked at other software for inspiration.
Big sources of inspiration for me would be software such as [Blender](https://www.blender.org/), [Notch](https://www.notch.one/) and [Godot](https://godotengine.org/).
In particular, I was wondering how to create a system that'd be both easy to use and expressive enough to allow the creation of interesting and emergent effects rather than simply ticking available engine features on or off...

Towards the end of 2019, something somewhat cliked in my mind;
I'd design the system to have only two types of nodes (okay, three) and they'd work like this:
- The **root node** from which the graph traversal would start at runtime.
- The **data node** representing a piece of data of a given type.
- The **component node** that can be attached to a data node to modify it.

<div style="text-align: center;">

![node-types](/node-types.png)\
*The different node types.*

</div>

\
This seemingly simple setup seemed to open up a lot of possibilities. :slightly_smiling_face:

I could have some "geometry data node" being nothing more than an index and a vertex buffer (plus some bounding box and probably other things...) while a "component node" connected to it would act as some kind of vertex shader that could be used for displacement purposes.

Similarly, a "shading data node" would represent a standard material while a "component node" would be some piece of code to be injected into the fragment shader for various per-pixel procedural shading operations.

Here's a collection of the different node categories in the engine at the time of writing:

<!--Mention something about the color scheme?-->

<div style="text-align: center;">

![node-categories](/node-categories.png)<br/>
*The available node categories in RogueEngine.*

</div>

\
The great thing about this approach is that the dependent nodes do not need to know how the data from the data node came to be (in the case of the geometry category, it could be a [procedurally-generated](https://en.wikipedia.org/wiki/Procedural_generation) mesh, geometry loaded through some [glTF](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) file, or even [metaballs](https://en.wikipedia.org/wiki/Metaballs) generated from the particle system), the format of the data node being fixed (in this case, an index and a vertex buffer...), we always know how to operate on it. :slightly_smiling_face:

<div style="text-align: center;">

<img src="/metaballs-nodes.png" width="49%" />
<img src="/metaballs.jpg" width="49%" /><br/>
<em>Metaballs created using the nodes, a classic of the demoscene.</em>

</div>

### Data model vs. GUI code

Now that I knew how my node system would operate, I had to find how to implement it.
My plan was to use [Dear ImGui](https://github.com/ocornut/imgui) for the UI because it's a joy to use and, I have to admit, I had little intention of investigating other GUI solutions.
ImGui is actually a great fit I found to creating such a creative UI system, and here's how I tackled it.

The main insight to take away in my opinion is the need to separate the data (what I'd call the **data model**) from the UI logic (often referred to as the **[view](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)**).
Having such a separation naturally implies creating an interface for iterating the data inside your "project" that can then be used both by the runtime, when playing back the demo content, and the ImGui code, when running the editor.

Our first step should therefore be to define that data model, so here it is:

<div style="text-align: center;">

![data-mode](/data-model.png)<br/>

*Data model for RogueEngine's runtime.*

</div>

\
There are essentially only three types of resources that a user can interact with through the interface and manipulate within a project (along with a few more as detailed below...):
- **Assets**: All your imported 3D models, textures, music files, etc.
- **Layers**: These allow to group nodes, mostly to facilitate multi-scene projects.
- **Nodes**: Nodes belong to their parent layer and can be executed by the runtime.

You may notice more blablabla.

Furthermore blablabla.

**Ranges** represent the series of time segments for when a particular resource is active on the timeline, while **Properties** represent, as the name suggests, the properties of a given node such as values and colors, links to assets and/or other nodes, etc.

<div style="text-align: center;">

![timeline](/timeline.jpg)

*The timeline panel allows the edition of ranges, i.e., when is a node or layer active.*

</div>

\
Finally, such a setup makes it rather simple to implement dreaded (but oh so useful!) features such as undo/redo.
I picked the same approach than [@voxagonlabs](https://blog.voxagon.se/2018/07/10/undo-for-lazy-programmers.html) and went ahead with serializing the whole project on every change to the data model.
This may sound rather inefficient (and I'm sure it won't hold up past certain project sizes...) but there isn't really all that much data we typically have to serialize when saving a project.
So it's definitely good enough for now and makes undo/redo indeed trivial to manage. :slightly_smiling_face:

### Animating the scene

etc.

### Code nodes

Going back to my earlier comment on many node systems being about "coding with nodes", I feel that the approach presented here manages to propose an alternative solution, blablabla

The system still makes it possible to use code for these moments where built-in nodes aren't quite enough.
Instead of "coding with nodes" however, here you'd simply create a "code node" letting you actual code inside a text field (or copy/paste from [Shadertoy](https://www.shadertoy.com/)...).
You can then add "bindings" to create tweakable properties that can be accessed directly from the code.

<div style="text-align: center;">

![code-node](/code-node.gif)\
*Code node with animated binding.*

</div>

\
Oh, and these properties are just like any other node property, so they too can be animated and/or plugged into other nodes... :slightly_smiling_face:

### Conclusion

I hope this overview was useful, don't hesitate to [reach out](https://twitter.com/guitio2002) or leave a comment!
