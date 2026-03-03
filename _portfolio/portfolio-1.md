---
title: "Unreal Vine Maker Tool"
excerpt: "Unreal in-editor tool that creates realistic vine meshes"
collection: portfolio
---

The goal was to create a tool that would automatically generate vine foliage to use as a decoration for walls in a level.

<br/><img src='/images/vines.png'>

**Initial problems** 
The original workflow would have requested to model the vines by hand to add them to the level. This process has a few limitations:

- Modeling foliage is time-consuming
- DCC modeled vine assets don't ensure full aesthetic compatibility with every asset
- If foliage cards are used for the leaves and the flowers, it potentially introduces some overdraw, especially if we are looking to create denser vine assets
- If polygons are used instead of cards, the polycount of the scene goes up significantly which is not efficient for a decorative asset. Moreover, polycount is difficult to manage and adjust in this scenario.
- Foliage density, branch direction and foliage species are often definitive once the asset has been exported.

**The Vine Maker tool solution**
- The tool is based on a Click-Drag scriptable tool, and Editor Utility Widget and a small PCG system.
- In a nutshell, the user draws a spline to represent the main stem of the vine. The tool produces a main stem that will stick to the surface of the wall and generate the secondary branches. Later, leaves and flowers are added thanks to the PCG system.
- Now, the vines are customized for each wall asset and the flowers and leaves are static mesh instances.

**Ease of use: one window UI and minimal controls**
- The most useful controls for this workflow live on the editor as buttons and parameters so that the user does not need to go inside the blueprint in order to customize the vine. I've kept the amount of buttons created via a Editor Utility widget very minimal to keep the tool easy to use. Furthermore, the parameters are set with sliders and sensible default values. 

**Functioning**
1. The user draws one or several splines on a wall which spawns temporary splines and meshes actors for the vine “skeleton”.
2. When they are satisfied and press “complete”, static meshes of vine skeletons corresponding to each spline that was drawn are saved to a folder.
3. The user now presses the button “Generate/Update vines” on the EUW. The vine skeletons in the folder are turned into an actor in the level which is set to receive the leaves and flowers from the PCG system. The final vines appear.
4. If the user isn't satisfied with the final look, instead of generating the vines they can click “Cancel Current Vines”. Alternatively, if the vines have already been generated, they can click on the “Open Vine folder” button and choose individual vines to delete.
Then, click again on “Generate/Update vines” to see the changes reflected in the level.
5. To completely start over the set of vines, the button “Clear Folder and Delete Vines” can be used.

**Technical Summary**

Entire graph view:

1/2
<br/><img src='/images/Vine%20-%20Full%20Graph%201.png'>

2/2
<br/><img src='/images/Vine%20-%20Full%20Graph%202.png'>

The Scriptable Tool uses Geometry Script to generate the vine with processes such as:

- Line traced spline creation to detect actor surfaces
- Custom noise to displace branches in order to simulate natural growth

Noise implementation 1/2

<br/><img src='/images/Vine%20-%20Full%20Graph%20-%20Noise%201.png'>

2/2

<br/><img src='/images/Vine%20-%20Full%20Graph%20-%20Noise%202.png'>

- Then, the EUW picks up the created mesh actors, creates static mesh assets and triggers the PCG system for the leaves and the flowers.

**Tool Limitations**

- As of now, the tool is best suited to work on walls (straight or curved) and handles corners as well.
- While I had explored ways to make it usable on smaller and more complex objects, it made more sense to first limit its use to walls. Then to later create another similar tool that would specifically fulfill other needs if they arise.
- The secondary branches generation is mostly based on randomly picked values and noise functions; thus the generated vines are not tweakable as of now. If the vine shape isn't satisfactory - the user needs to cancel the vine and redraw a new spline to generate another random vine. Thankfully, this only takes a couple seconds.

**Performance**
- The main element that elongates processing times is the “amount of branches” parameter (80 branches per vine seemingly being the sweet spot and anything above 120 starting to slow the editor down too much).
- Also, although it is possible to draw many splines in one go, this can slow down the generation process. As a consequence, it is better to press “complete” every couple of vines. It also separates the static mesh assets more efficiently in the folder.
- On average, the computation for the vines (80 branches, 1.7m max branch length) takes 4.1 seconds (including the time the user takes to draw the spline) and the mesh spawn using the EUW 1.1ms.

Spline Creation

Static Mesh Actor spawning

Static mesh asset creation

PCG Profiler

In normal conditions, the tool works pretty smoothly. In edge cases, where there is a lot of splines drawn and meshes generated at the same time, the processing times can take several minutes or may completely freeze the editor. 

As a consequence I am considering limiting the tool to only draw one spline at a time even though it could be quite restrictive.

All in all, the tool does add a significant amount of triangles to a scene (at least 20K for a very simple vine) but not necessarily more than a regular vine asset. On top of that the user has some control over the final tri count through the tool parameter “Branches Tris Resolution”.

**Tool Expansion**
Scaling: The next step could be to allow for the creation of multiple sets of vines attached to multiple PCG systems. This would make the tool fit for larger levels since it would be possible to have different species of vines.

Improving the UI: I will be looking to get some feedback from artists to evaluate the actual ease-of-use of the tool.
