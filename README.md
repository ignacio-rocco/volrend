# N3-Tree Volume Rendering

This is a real-time volume renderer built using OpenGL.
Here, we use a N^3 tree to model volumetric emission and absorption at RGB frequencies
(by N^3 tree we mean octree but with branching factor N, the standard case is N=2).

Similar to NeRF, each voxel cell contains `(r,g,b,sigma)`, and
alpha composition is applied via the rule `1 - exp(-length * sigma)`.
A variant of the DDA ray tracing algorithm is used to compute the length within each grid cell;
performance improvement may be possible..

## Build
Build using CMake
```sh
mkdir build && cd build
cmake ..
make -j12
```

- Pass `-DVOLREND_USE_CUDA=ON` to use CUDA-OpenGL interop renderer.
- Pass `-DVOLREND_USE_CUDA=OFF` to use GLSL shader backend.
It is slower but can work without CUDA (e.g. AMD GPU).

On Ubuntu, to get the dependencies, try
`sudo apt-get install libgl1-mesa-dev libxi-dev libxinerama-dev libxcursor-dev libxrandr-dev libgl1-mesa-dev libglu1-mesa-dev`

## Run
```sh
./volrend <name>.npz
```
For LLFF scenes, we also expect
```sh
<name>_poses_bounds.npy
```
In the same directory. This may be copied directly from the scene's `poses_bounds.npy` in the LLFF dataset.

Note: In older octrees, an OpenEXR file was used to store the image data, this was
```sh
<name>_data.exr
```
In the same direcotry.

### Dependencies
- C++14
- OpenGL
    - any dependencies of GLFW
- libpng-dev (only for writing image in headless mode)

#### Optional
- CUDA Toolkit, I use 11.0
    - Pass `-DVOLREND_USE_CUDA=OFF` to disable it.


## Web

Install emscripten per instructions here:
https://emscripten.org/docs/getting_started/downloads.html

Then use
```sh
mkdir embuild && cd embuild
emcmake cmake ..
make -j12
```

The full website (including html/css) should be written to `embuild/build`. To launch it locally for previewing, you can use the make target:
```sh
make serve
```
Which should launch a server at http://0.0.0.0:8000/
