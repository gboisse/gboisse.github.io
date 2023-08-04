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
I really wanted to be able to team up with artists and designers (or at least people with skills complementary to mine) so having an interface that everybody could use regardless of programming knowledge seemed to make sense.
What wasn't so obvious to me however was what kind of interface I should go for...
<!--it seemed to made sense to invest in an interface that everybody could use regardless of programming knowledge.-->

I was not particularly fond of node graphs initially for two reasons:
- Many of these systems are really what I'd call "coding with nodes"; so you still get the complexity of programming only more inconvenient.
- It can get real messy real fast.

<div style="text-align: center;">

![blueprint-from-hell](/blueprint-from-hell.png)\
*Courtesy of [blueprints from hell](https://blueprintsfromhell.tumblr.com/).*

</div>

\
Node systems were still appealing to me however for different reasons;
they can be more intuitive and less intimidating than other solutions and they tend to look good on screenshots.
Then maybe it'll be easier for me to convince artists to get on board? :slightly_smiling_face:

So I started thinking about a node system that would **not** be "coding with nodes".

### Node system

I had little to no experience with node-based systems when starting, so I naturally went and look at other pieces of software for inspiration.
Big sources of inspiration for me would be software such as [Blender](https://www.blender.org/), [Notch](https://www.notch.one/) and [Godot](https://godotengine.org/).
In particular, I was wondering how to create a system that'd allow for some amount of creativity as opposed to ticking available engine features on or off.
This meant that the graph should be fairly expressive somehow.
<!--would allow for composing scenes blablabla creating interesting and emergent effects, that is ....-->

Towards the end of 2019, I had somewhat of a eureka moment.
I'd design the system to have only two types of nodes (okay, three) and they'd work like this:
- The "root node" from which the graph traversal would start at runtime.
- The "data node" representing an inert piece of data of a given type.
- The "component node" that can be attached to a data node to modify its state.

<div style="text-align: center;">

![node-types](/node-types.png)\
*The different node types.*

</div>

\
For instance, in the "geometry" category, a data node would be nothing more than an index and a vertex buffer (plus some bounding box and probably other things...) while a component node would be some kind of vertex shader that could be connected to it for displacement purposes.

Similarly, in the "shading" category, the data node would be a material while a component node would be a piece of fragment shader that could be applied for procedural shading operations.

<div style="text-align: center;">

![node-categories](/node-categories.png)<br/>
*The available node categories in RogueEngine.*

</div>

\
The great thing about this approach is that the dependent nodes do not need to know how the data from the data node came to be (in the case of the geometry category, it could be a procedurally-generated mesh, some geometry loaded from a [glTF](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) file, or some metaballs generated from a particle system), the format of the data node being fixed, we therefore always know how to operate on it. :slightly_smiling_face:

This allows for all kinds of connections between the different nodes and features as well as creating effects combining different kinds of primitives (geometry, particles, etc.), which would allow hopefully for more advanced and interesting visuals.
<!--In this sense, this is a "data-oriented" node design.-->
<!--This allows for conecting nodes in all kinds of way therefore achieving a high degree of expression and flexibility in creating the scene content.-->

### Data model vs. GUI code

Now that I knew how my node system would operate, I had to find how to implement it.
My plan was to use [Dear ImGui](https://github.com/ocornut/imgui) for the UI because it's a joy to use and, I have to admit, I had little intention of investigating other GUI solutions. ImGui is actually a great fit I found to creating such a creative UI system, and here's how I tackled it.

The main insight to take away in my opinion is the need to separate the data (what I'd call the **data model**) from the UI logic and code (often referred to as the **[view](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)**).
Having such separation naturally implies creating an interface for iterating the data inside your "project" that can then be used both by the runtime, when playing back the demo content, and the ImGui code, when running the editor.
<!--It also makes it fairly straightforward to implement features such as undo/redo (more on this later...). :slightly_smiling_face:-->

Our first step should therefore be to define that data model, so here it is:

<div style="text-align: center;">

![data-mode](/data-model.png)<br/>

*Data model for RogueEngine runtime.*

</div>

\
There are essentially only 3 types of resources that the user can interact with through the interface and manipulate in a project:
- **Assets**: 3D models, texures, music files, etc.
- **Layers**: These represent groups of nodes.
- **Nodes**: Nodes belong to their parent layer.

**Ranges** represent the series of *[start, end]* segments for when a particular resource is active on the timeline, while **Properties** represent, as the name suggests, the properties of a given node such as values, colors, links to assets and/or other nodes, etc.
<!--This is arguably the most complex ...-->

<!--So the data model is really pretty straightforward.
When it comes to **assets** and **layers**, their properties are fixed (a name, a path to the file in the case of an asset and some unique ID) so serializing and deserializing the information for loading and saving purposes and/or editing their properties with ImGui is trivial.

Nodes are a bit more complex however as their properties blablabla.-->

### Serialization

One of the first things you'd need to implement setting up a system like this, is the ability to save and load project files.
In more technical terms, this means you'd need to be able to serialize the state of the project so it can be written it to disk for instance (saving), as well as deserializing some data blob into a functional runtime state (loading).

[Reflection](https://en.wikipedia.org/wiki/Reflective_programming) is often used to implement serialization and deserialization.
But do we really need it here?
Lookking at the data model presented above, most of our tables are fairly static.
The only exception would be the node's **properties**, which can indeed vary from node type to node type.
For the most part however, reflective programming isn't required and we can simply write some serialize and deserialization functions for an **Asset**, a **Layer**, and most of a **Node** and this already takes us 90% of the way. :slightly_smiling_face:

<div style="text-align: center;">

{{< highlight cpp >}}
class Project
{
public:
    Project();

    Result storeToArchive(Archive &archive);
    Result loadFromArchive(const Archive &archive);

private:
    SparseArray<Asset> assets;
    SparseArray<Layer> layers;
    SparseArray<Node> nodes;
};
{{< / highlight >}}
*Project serialization/deserialization API.*

</div>

\
Note the **Archive** type blablabla...

The **SparseArray** type isn't too important and could be replaced by other containers; in short, it's an array of stable keys, which storage is maintained compacted over the addition and deletion of objects.
You can find an example implementation over [here](https://github.com/gboisse/gfx/blob/43e47de5ff0a46f277e15d92f8c3f9ec4bd65763/gfx_core.h#L257-L500).

<!--
discuss about "database" approach...

Things are separate.

The "data model" is designed without the UI in mind.

Then, all we need to do, is to iterate all the items in our table (nodes in this case), and call the corresponding ImGui drawing function.
-->

### Undo/redo

etc.

### Animating the scene

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
