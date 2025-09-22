# <center>Metaballs</center>
I recreated metaballs!

## But wtf are metaballs??
`--> Metaballs are a computer graphics technique for creating organic, fluid-like shapes that merge and separate smoothly. They work by using an implicit function that defines a "field" of influence for each object. Rather than being defined by a fixed mesh of polygons, the surface of a metaball shape is the location where the combined influence of all the metaballs in the scene reaches a certain threshold.`

## The Core Concept: Implicit Surfaces
Imagine each metaball is a point in space emitting an invisible, decreasing force field. The closer you are to the center of a metaball, the stronger its influence. When you have multiple metaballs, their fields sum together.

The surface of the final blobby shape isn't a pre-defined mesh; it's an isosurface. An isosurface is a 3D surface where every point on it has the exact same value in a scalar field. In the case of metaballs, the scalar field is the sum of the influence fields, and the iso-value is the chosen threshold.

## The magic behind
there's no magic, magic isn't real, it's all Math! The mathematical function for a single metaball's influence at a point is often an inverse distance function, like:
# $$f(x,y,z) = \frac{R^2}{(x-x_0)^2+(y-y_0)^2+(z-z_0)^2}$$
Where:
 - $(x,y,z)$ is the point being evaluated.
 - $(x_0,y_0,z_0)$ is the center of the metaball.
 - $R$ is a constant that controls the radius and strength of the influence.

For a scene with multiple metaballs, the total field value at any point is the summation of the influence from all metaballs:
# $$F(x,y,z) = \sum_{i=1}^n \frac{R^2_i}{(x-x_i)^2+(y-y_i)^2+(z-z_i)^2}$$

The final surface is all the points where $F(x,y,z)=T$, where $T$ is the threshold value. When two metaballs get close, their influence fields overlap, and the summed value between them exceeds the threshold, creating the smooth, merged "blob" effect.

### Rendering the Surface: The Marching Cubes Algorithm
Since the metaball surface isn't a regular polygonal mesh, it needs to be generated. The most common way to do this for 3D metaballs is using the **Marching Cubes algorithm**.

#### Create a Grid
A large 3D grid of "cubes" is placed over the entire scene where the metaballs exist.

#### Evaluate Corners
The algorithm "marches" through each cube, evaluating the $F(x, y, z)$ function at all eight of its corners.

#### Generate a Lookup Index
Each corner is assigned a binary value: a `1` if its F value is above the threshold (inside the surface) and a `0` if it's below (outside). This creates an 8-bit index for the cube (e.g., `10010110`).

#### Lookup and Interpolate
The algorithm uses this index to look up a pre-calculated table that specifies which polygons (usually triangles) should be drawn inside that specific cube to approximate the surface. The exact vertex positions for these polygons are found by linearly interpolating between the "inside" and "outside" corners of the cube.

By processing every cube in the grid, the algorithm generates a seamless, polygonal mesh that accurately represents the metaball surface. The higher the grid resolution, the more detailed and smooth the resulting mesh will 
be.

<hr>

# The code result:
<img width="1367" height="701" alt="image" src="https://github.com/user-attachments/assets/e317ad45-7db3-4961-9276-4839783dd5cd" />
