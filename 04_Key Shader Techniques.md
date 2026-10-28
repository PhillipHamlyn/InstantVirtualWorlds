# Key Shader Techniques

## Threading
The typical GPU has thousands of parallel threads available. In the rasterisation pipeline (drawing to a screen or other surface) the threads are used to calculate the color of sets of pixels. Since no pixel can influence the colour of another pixle the rasterization pathway is natrually suited to parallelization.

The Instant Virtual World will use extensive non-rasterization GPU resources via compute shaders. A key challenge and performance factor is to use as many GPU threads as possible in any given set of calculations. This means inverting many traditional procedural coding techniques;
- As far as possible eliminating all loops , preferring to use parallel threads on the GPU, and breaking work into passes that explicitly prepare data that are parallel-friendly
- Removing the need to read GPU data back to the CPU wherever possible, and when unavoidable, reading it back several frames after it was generated to prevent GPU stalling
- Removing semaphores and cross-thread data dependencies, which reduce GPU threading efficiency

## Data Buffers
In traditional GPU rastertization pipelines data is sent to the GPU via specialised memory buffers from the CPU. These typically consist of 
- Vertex Buffers: containing geometry which will be transformed into rendered pixels
- Texture Buffers: containing rectangular memory spaces optimised to storing imagery and colours (but can contain other data) restricted to certain colour-oriented formats

In the Instant Virtual World we need to extend well beyond these traditional types and make extensive use of Structured Buffers. These are a monolithic set of data held as a table in GPU memory and able to be read and written to by HLSL code, and expressed as a table of Structs with a fixed binary layout.

The collection of Structured Buffers are interleated via shared index values so that a Shader can traverse data relating to their thread using indexes, offset and cross-reference indexes. This is comparable to non-relational database storage.

## Append/Consume Buffers
All Structured Buffers are defined as a fixed size on creation and cannot be extended or contracted. A specialised form of Structured Buffer allows appending and consuming of data within that fixed data space.

The current 'last used index' pointer is automatically maintained by DirectX as new records are Appended or the last record is Consumed. Programmatically this is a LIFO queue. 

The current 'last used index' is not available to the CPU but may be used as a parameter to calls to Shaders via the DispatchIndirect DirectX11 command, allowing some degree of control over execution.

## Interpolation Libraries
Because we are using Structured Buffers instead of Texture Buffers for most data storage, the built-in hardware interpolation features for Textures are not available. A robust library of functions to interplate several Structured Buffer records based on relative weighting is required.

This library also requires point-to-barycentric and barycentric-to-point functions for translating floating point locations within a triangle to the relevant Structured Buffer record index pointer and vice-versa.



