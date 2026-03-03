---
title: "The barn house"
excerpt: "A PCG building that with comes with parameters to create a simple and customized barn"
"
collection: portfolio
---

My goal was to build a tool that generates a building from a limited number of assets and inputs.

<img src='/images/barn%20thumbnail.png'>

**Ease of use**

The user is able to drag the PCG blueprint into the level and to customize the look of thebarn using the parameters. 

**PCG system**

1. Input : Blueprint's location in the level 
2. Creation of a vertical spline, its points being the barn's levels generation origins 
3. Barn Lower levels: Rectangle creation for each level to spawn the walls
4. Addition of the barn extensions, windows and various beams
5. Lower roof positioning and scaling 
6. Rectangle creation for the top levels
7. Top roof positioning and scaling 
8. Cupolas positioning 

Rules for visual consistency:

- The extensions are spawned only on the sides and at the bottom level of the barns.
- Only one higher level is added on top.
- The roofs are scaled and later their textures are world aligned.

**PCG System Architecture** 

- Main vertical spline generation
    - Barn Lower levels subgraph
- Lower roof generation
    - Barn Higher levels subgraph
- Higher level roof generation
    - Higher roof horizontal beam subgraph
- Cupolas generation

Barn Building — Main Graph

<img src='/images/Barn%20-%20PCG%20-%20Full%20Graph.png'>

Barn Lower Levels — Subgraph
<img src='/images/Barn%20-%20PCG%20-%20Lower%20Levels.png'>

Main Graph > Cupola Section — Custom HLSL
<img src='/images/Barn%20-%20PCG%20-%20Cupola.png'>

Custom HLSL: Point Generation 

```HLSL
// Getting and naming the input variables

float3 RoofPosIni = PosIni_GetFloat3 ( Out_DataIndex, 0,'PosIni');
int BaUnit = BUnit_GetInt( Out_DataIndex, 0, 'BarnUnit');
int BaX = BX_GetInt(Out_DataIndex, 0, 'BarnX');


// Naming useful values

float3 Pos;

float CupolaHeightOffset = 40;

float FullRoof = (BaUnit * BaX);
float HalfRoof = (BaUnit * (BaX/2));

// Cupolas' Positions

Pos.x = ((((FullRoof / NumElements)      // Space between cupolas
            * ElementIndex)              // The cupolas' relative x position
            + RoofPosIni.x)              // Adding the barn's world x position 
            - HalfRoof)                  // Centering
           + (HalfRoof/NumElements);     // Adding offset
            
Pos.y = RoofPosIni.y;

Pos.z = RoofPosIni.z + CupolaHeightOffset;

// Output

Out_SetPosition(Out_DataIndex,  ElementIndex, Pos);

```

**Performance**:

Thanks to the simplicity of system and meshes, spawn time and memory usage are very minimal.

PCG Profiling:

**Limitations** **and potential improvements** 

- **Barn rotation:** when trying out the tool I realised it was not possible to rotate the barns on the Z axis due to how the higher levels’ and cupolas’ world positions were calculated. I'm currently working on this in order to allow for more flexibility.
- **Adding interiors**
- **A Whole village:** Creating another PCG ****system to spawn several barns at the same time and allowing for the randomization of the UI parameters

**Art breakdown** 

Barn Mini Kit — modeled in Blender :

**Materials** — **Textures**

Barn Walls


Exterior wall material for the barns (intact version)

The graph also exports a texture for moss stains and another one for chipped paint to be added and controlled in engine

Unreal Master Material :

**Roof Tiles**

Substance Designer graph
