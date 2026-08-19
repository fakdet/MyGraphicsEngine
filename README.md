# MyGraphicsEngine

A custom OpenGL graphics engine written in C++, built as a learning project to get hands-on with real-time 3D rendering, scene management, and shader pipelines — with a live ImGui editor layered on top for tweaking everything at runtime.

This is my first project built for getting into graphics programming, so the codebase is intentionally hand-rolled rather than leaning on a heavier framework: custom transform math, a JSON-driven shader/model system, and a small scene graph, all built from scratch on top of GLFW + GLAD + OpenGL.

## Features

- **Runtime scene editor (ImGui)** — Two editor panels running live alongside the 3D view:
  - **Update panel** — select any model or camera in the scene and edit its shape, texture, position, rotation, scale, and texture tiling with sliders in real time.
  - **Create panel** — spawn new shapes (square, circle, cube, pyramid, frustum, cylinder) with a chosen texture, or spin up additional cameras with custom FOV/near/far/aspect settings.
- **Multiple simultaneous cameras** — the scene renders from an active camera in the main viewport and a second camera in a picture-in-picture viewport at the same time, each with independently adjustable projection settings.
- **Camera visualization** — every camera in the scene is rendered as a wireframe frustum model, so you can see where each camera is and how it's oriented from any other camera's view.
- **Data-driven shaders and models** — shader programs and models are defined in JSON (see `settings.json` per shader, and per-model `.json` files), describing which vertex/fragment shader files to compile and which uniforms to bind, so new shaders/materials can be added without touching engine code.
- **Texture management** — a singleton `TextureManager` loads and caches textures from disk (`stb_image`) by filename, so repeated model creation doesn't reload/re-upload the same texture to the GPU.
- **First-person camera controls** — WASD movement, Space/Shift for up/down, and mouse look, with a toggle to release the cursor for interacting with the ImGui panels (`F10`/`F11`).
- **Simple animated object** — a pyramid model that bobs and spins above the currently selected model, demonstrating basic per-frame transform animation independent of the editor.

## Built With

- **C++** — core engine
- **OpenGL 3.3 (Core Profile)** — rendering backend
- **GLFW** — windowing, input, and OpenGL context creation
- **GLAD** — OpenGL function loader
- **Dear ImGui** — in-app editor UI (via the GLFW + OpenGL3 backends)
- **GLM** — vector/matrix math
- **nlohmann/json** — parsing shader and model definition files
- **stb_image** — texture loading
- **CMake** — build system

## Getting Started

### Prerequisites

- CMake 3.24.1+
- A C++ compiler targeting the MSVC ABI (this project was built and tested with Clang on Windows, linking against the MSVC runtime — `cl.exe` / Visual Studio Build Tools work equally well)
- Windows (current build setup targets Windows; GLFW/link paths would need adjusting for other platforms)

### Third-party libraries

This repo does **not** vendor its dependencies — you'll need to fetch them yourself into a `lib/` folder that sits two directories above the project root (i.e. alongside the parent of this repo), matching the paths in `CMakeLists.txt`:

```
lib/
├── GLFW/
│   ├── include/GLFW/       # glfw3.h, glfw3native.h
│   └── lib/                # glfw3.lib (+ glfw3.dll if using the dynamic build)
├── glm/
│   └── glm/                # GLM headers
├── imgui/                  # Dear ImGui source, including backends/
├── json/
│   └── nlohmann/json.hpp   # nlohmann/json single header
└── stb/
    └── stb_image.h
```

Steps:

1. **GLFW** — download the prebuilt Windows binaries from [glfw.org/download.html](https://www.glfw.org/download.html). Use the `include/` folder as-is, and copy the `lib-vc2022` (or your matching MSVC toolset) folder's contents into `lib/GLFW/lib/`.
2. **GLM** — clone `https://github.com/g-truc/glm.git` into `lib/glm`.
3. **Dear ImGui** — clone `https://github.com/ocornut/imgui.git` into `lib/imgui`.
4. **nlohmann/json** — download `json.hpp` from [github.com/nlohmann/json/releases](https://github.com/nlohmann/json/releases) into `lib/json/nlohmann/json.hpp`.
5. **stb_image** — download `stb_image.h` from [github.com/nothings/stb](https://github.com/nothings/stb) into `lib/stb/`.

### Building

```powershell
cmake -DCMAKE_BUILD_TYPE=Debug -S . -B ../newbuild
cmake --build ../newbuild
```

The resulting executable will be at `../newbuild/Debug/testproject.exe`.

## Controls

| Input | Action |
|---|---|
| `W` / `A` / `S` / `D` | Move camera forward / left / backward / right |
| `Space` | Move camera up |
| `Left Shift` | Move camera down |
| Mouse movement | Look around |
| `F10` | Lock cursor (camera look mode) |
| `F11` | Unlock cursor (interact with ImGui panels) |

## Adding New Content

- **New model**: create a `.json` file under `models/` with `ModelName`, `TextureName`, `ShaderProgramName`, `ShapeType`, `Position`, and `Scale`, then load it in code via `Scene::addModel(filename)`.
- **New shader**: create a folder under `shaders/` with a `settings.json` describing the program name, vertex/fragment shader filenames, and uniform list, alongside the GLSL source files.
- **New texture**: drop the image file into `images/` and reference it by filename — `TextureManager` handles loading and caching automatically.

## Author

Built by [fakdet](https://github.com/fakdet).
