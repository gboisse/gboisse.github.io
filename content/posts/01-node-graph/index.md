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
I may talk about the engine itself in another post but for now I'd like to focus on its UI. <!-- :grinning_face_with_big_eyes: -->

As a teaser, here's a screenshot of the tool running our Revision 2023 demo:

<div style="text-align: center;">

![node-graph](/node-graph.jpg)<br/>
*Intro sequence from [Reality Check](https://www.pouet.net/prod.php?which=94177).*

</div>

<!-- If you're interested in knowing how to achieve this using Dear ImGui, then keep reading :winking_face: -->

### A bit of background

I started thinking about this project back in 2019 with the aim of releasing some PC demo productions.
I really wanted to be able to team up with artists and designers, so it seemed to make sense to have an interface that everybody could use regardless of programming knowledge.
What wasn't so obvious to me however was what kind of interface I should go with...

I was not particularly fond of node graphs initially for two reasons:
- Many of these systems are really "coding with nodes"; so you still get the complexity of programming only more inconvenient.
- It can get real messy real fast.

<!--
I started thinking about building a visual editor over my existing OpenGL engine back in 2019 but was initially fairly reluctant to the idea of using nodes.
I tend to see many node graph systems as coding, but with nodes, often resulting in what can be best described as an undecipherable mess.
-->

<div style="text-align: center;">

![blueprint-from-hell](/blueprint-from-hell.png)<br/>
*Courtesy of [blueprints from hell](https://blueprintsfromhell.tumblr.com/).*

</div>
<br/>
Node systems were still appealing to me however for different reasons;
they can be more intuitive and less intimidating than text-based programming and they tend to look better on screenshots.
Then maybe it'll be easier for me to convince artists to get on board? :slightly_smiling_face:

<!--
Such systems do present some appealing aspects though;
if well made, they can be far more intuitive than a text-based coding system making them less intimidating to get started with.
Then maybe it'll be easier for me to convince artists to get on board? :slightly_smiling_face:
-->

So I started thinking about a node system that would <b>not</b> be "coding with nodes".

<!--
Why did I finally go back to nodes?
I wanted to make sure my node system would <b>not</b> be a visual coding one.
-->

### Data oriented nodes

Having little experience of node-based systems, I first started to explore programs such as [Blender](https://www.blender.org/), [Notch](https://www.notch.one/), [Godot](https://godotengine.org/), etc.
I was especially wondering how to create a system that would allow to create interesting effects that I would not necessarily have thought about rather than a list of effects users could "tick" on and off.

Some kind of eureka moment.
I'd have two types of nodes (okay, three):
- The "root node" from which the graph traversal starts at runtime.
- The "data node" representing an inert piece of data of a given type.
- The "component node" that can be attached to a data node to modify its state. <!--(an effect if you will).-->

<div style="text-align: center;">

![node-types](/node-types.png)<br/>
*The available node types.*

</div>
<br/>
For instance, in the "geometry" category, a data node is nothing more than an index and a vertex buffer (plus some bounding box and other things...) while a component node would be some kind of vertex shader that can be applied for displacement purposes.

Here's the basic idea blabla

<!--
### Setup

When I started thinking about this system back in 2019, I was wondering how to produce a system that would be both simple to code yet expressive enough that it could lead to creating interesting visuals.

One day I stumbled upon a solution that I thought could be interesting...
-->

### Data model vs. GUI code

discuss about "database" approach...

Things are separate.

The "data model" is designed without the UI in mind.

Then, all we need to do, is to iterate all the items in our table (nodes in this case), and call the corresponding ImGui drawing function.

### Undo/redo

etc.

### Code nodes

The system still makes it possible to use code for these moments where built-in nodes aren't quite enough.
Instead of "coding with nodes" however, here you'd simply create a "code node" letting you type (GLSL) code inside a text field (or copy/paste from [Shadertoy](https://www.shadertoy.com/)...).
You can then add "bindings" to create tweakable properties that can be accessed directly from the code.

<div style="text-align: center;">

![code-node](/code-node.gif)<br/>
*Code node with animated binding.*

</div>
<br/>
Oh, and these properties are just like any other node property, so they too can be animated and/or plugged into other nodes... :slightly_smiling_face:
