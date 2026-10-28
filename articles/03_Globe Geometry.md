# Globe Geometry

## Coordindate Systems

A globe can be represented as a Geoid (the basic sphere) with a given radius, plus the height at a given point. 

This corresponds with our normal understanding of global coordinates based on latitude and longitude (the position on the Geoid surface) plus the height above sea level.

Since our globe will be Earth sized a nominal Radius of 1,000,000 meters is used. Within the framework the standard unit size will be meters. 

For a global sized real-world coordinate system precision is an issue. Irrespective of what standard unit size is chosen, the standard 32-bit float cannot contain enough precision for stable maths at global scale.

On the CPU Double (64-bit) accuracy is available to us, but the GPU is optimised for 32-bit float operations.

In order to have a stable calculation system we need to adopt a multi-tier coordinate system. For Latitude and Longitude calculations the coordinate system is three tier - Hours, Minutes, Seconds giving enormous accuracy. 

We will use a two-tier coordinate system based on 
- A Datum Coordinate expressed in units of 2048 meters.
- A Relative Coordinate expressed in units of 1 meter.

At all stages care must be taken to work in the most appropriate coordinate level, and only combine the two coordiates by addition when the resulting calculation can tolerate low levels of precision. In general whenever passing coordinates between components, both the Relative and Absolute coordinate will be passed.

## Geometric Subdivision

Almost all computer based landscape models use a square grid to represent an area of the world. This only works on a local level, since 
- The globe is not a flat surface, so the flat surface does not incorporate globe curvature
- You cannot divide a globe into squares, so you cannot do accurate subdivision on a global scale

The global subdivision scheme we use is the IcoSphere. This is a method of subdivision of a sphere based on Equilateral Triangles

![An Icosphere](https://philliphamlyn.wordpress.com/wp-content/uploads/2025/10/image-2.png?w=300)

The advantages of this approach are
- It is infinitely divisible
- Maths techniques for coordinates within an Equilateral Triangle are widely used (Barycentric Coordinates)
- The orientation of one Triangle with respect to a neighbour can be computed mechanically

The disadvantages are
- Most published techniques for procedural landscape assume square tiles
- Some GPU hardware is optimised for rectangular resources, expressed as Textures