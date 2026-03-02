---
title: "Unreal Vine Maker Tool"
excerpt: "Unreal in-editor tool that creates realistic vine meshes"
collection: portfolio
---

The goal was to create a tool that would automatically generate vine foliage to use as a decoration for walls in a level.

<br/><img src='blob/master/images/vines.png'>

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
