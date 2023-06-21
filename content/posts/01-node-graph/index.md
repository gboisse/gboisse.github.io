---
slug: node-graph
title: Visual node graph with ImGui
date: 2023-06-19
author: Guillaume Boissé
description: How to use Dear ImGui for creating a visual node graph.
draft: true
---

<div style="text-align: justify">

I wanted my first post on this blog to be about the node graph system that I created for my personal graphics engine called "RogueEngine".
I may talk about the engine itself in another post but for now I'd like to focus on its UI. <!-- :grinning_face_with_big_eyes: -->

As a teaser, here's a screenshot of the tool running our Revision 2023 demo:

<div style="text-align: center;">

![node-graph](/node-graph.jpg)
*Intro sequence from [Reality Check](https://www.pouet.net/prod.php?which=94177).*

</div>

<!-- If you're interested in knowing how to achieve this using Dear ImGui, then keep reading :winking_face: -->

### Setup

When I started thinking about this system back in 2019, I was wondering how to produce a system that would be both simple to code yet expressive enough that it could lead to creating interesting visuals.

One day I stumbled upon a solution that I thought could be interesting...

### Data model vs. GUI code

discuss about "database" approach...

Things are separate.

The "data model" is designed without the UI in mind.

Then, all we need to do, is to iterate all the items in our table (nodes in this case), and call the corresponding ImGui drawing function.

### Undo/redo

etc.

### Code nodes

blablabla.
