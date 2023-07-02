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

As a teaser, here's a screenshot of the tool running our Revision 2023 demo:

<div style="text-align: center;">

![node-graph](/node-graph.jpg)\
*Intro sequence from [Reality Check](https://www.pouet.net/prod.php?which=94177).*

</div>

### A bit of background

I started thinking about this project back in 2019 with the aim of releasing some PC demo productions.
I really wanted to be able to team up with artists and designers, so it seemed to make sense to have an interface that everybody could use regardless of programming knowledge.
What wasn't so obvious to me however was what kind of interface I should go with...

I was not particularly fond of node graphs initially for two reasons:
- Many of these systems are really "coding with nodes"; so you still get the complexity of programming only more inconvenient.
- It can get real messy real fast.

<div style="text-align: center;">

![blueprint-from-hell](/blueprint-from-hell.png)\
*Courtesy of [blueprints from hell](https://blueprintsfromhell.tumblr.com/).*

</div>

\
Node systems were still appealing to me however for different reasons;
they can be more intuitive and less intimidating than text-based programming and they tend to look better on screenshots.
Then maybe it'll be easier for me to convince artists to get on board? :slightly_smiling_face:

So I started thinking about a node system that would <b>not</b> be "coding with nodes".

### Data-oriented nodes

Having little experience of node-based systems, I first started to explore programs such as [Blender](https://www.blender.org/), [Notch](https://www.notch.one/), [Godot](https://godotengine.org/), etc.
I was especially wondering how to create a system that would allow for creating interesting effects that I would not necessarily have thought about rather than a list of effects users could "tick" on and off.

Some kind of eureka moment.
I'd have two types of nodes (okay, three):
- The "root node" from which the graph traversal starts at runtime.
- The "data node" representing an inert piece of data of a given type.
- The "component node" that can be attached to a data node to modify its state.

<div style="text-align: center;">

![node-types](/node-types.png)\
*The different node types.*

</div>

\
For instance, in the "geometry" category, a data node would be nothing more than an index and a vertex buffer (plus some bounding box and probably other things...) while a component node would be some kind of vertex shader that could be applied for displacement purposes.

Similarly, in the "shading" category, the data node would be a material while a component node would be a piece of fragment shader that could be applied for procedural shading operations.

<div style="text-align: center;">

![node-categories](/node-categories.png)<br/>
*The available node categories in RogueEngine.*

</div>

\
The great thing about this approach is that the dependent nodes do not need to know how the data from the data node came to be (in the case of the geometry category, it could be a procedurally-generated mesh, some geometry loaded from a [glTF](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) file, or some metaballs generated from a particle system), the format of the data node being fixed, we therefore always know how to operate on it. :slightly_smiling_face:

In this sense, this really is a "data-oriented" node design.

### Data model vs. GUI code

Now that I knew how my node system would operate, I had to find how to implement it.
My plan was to use [Dear ImGui](https://github.com/ocornut/imgui) for the UI cause it's great and, I have to admit, I had little intention of investigating other GUI solutions. :slightly_smiling_face:

ImGui is actually a great fit I found to creating such a creative UI system, and here's how I tackled the problem.

<!--
discuss about "database" approach...

Things are separate.

The "data model" is designed without the UI in mind.

Then, all we need to do, is to iterate all the items in our table (nodes in this case), and call the corresponding ImGui drawing function.
-->

### Undo/redo

etc.

### Code nodes

The system still makes it possible to use code for these moments where built-in nodes aren't quite enough.
Instead of "coding with nodes" however, here you'd simply create a "code node" letting you type (GLSL) code inside a text field (or copy/paste from [Shadertoy](https://www.shadertoy.com/)...).
You can then add "bindings" to create tweakable properties that can be accessed directly from the code.

<div style="text-align: center;">

![code-node](/code-node.gif)\
*Code node with animated binding.*

</div>

\
Oh, and these properties are just like any other node property, so they too can be animated and/or plugged into other nodes... :slightly_smiling_face:
