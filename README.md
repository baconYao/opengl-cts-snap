# OpenGL CTS Snap

This snap provides an easy way to install and run the tests found in
[Khronos's OpenGL Conformance Test Suite](https://github.com/KhronosGroup/VK-GL-CTS)
(the `glcts` binary, built for the `surfaceless` EGL target).

## Snap bases

The snap is maintained for multiple bases, each in its own self-contained
snapcraft project directory:

| Directory | Base   | GPU content       | Arches       | Notes                                          |
|-----------|--------|-------------------|--------------|-------------------------------------------------|
| `core22/` | core22 | `graphics-core22` | amd64, arm64 | Toolchain and drivers from the 22.04 archive     |
| `core24/` | core24 | `gpu-2404`        | amd64, arm64 | Toolchain and drivers from the 24.04 archive     |
| `core26/` | core26 | `gpu-2604`        | amd64, arm64 | Newer toolchain and drivers from the 26.04 archive |

Each project cross-compiles for `arm64` from an `amd64` build host (as well as
building natively on an `arm64` host), so a single `amd64` builder can produce
both architecture's snaps.

Newer hardware needs newer userspace drivers. If a test fails to find a
usable GPU, the base you installed likely predates your GPU; use a newer
base.

In the Snap Store the variants are published on separate tracks
(`latest`/default for core24, `core22` for core22, `core26` for core26).

## Build

Each directory is a directly-buildable snapcraft project. `cd` into the base
you want and run snapcraft:

```
cd core22 && snapcraft pack
cd core24 && snapcraft pack
cd core26 && snapcraft pack
```

Each project declares `platforms: amd64, arm64`; pass `--platform=arm64` (or
build on/for an arm64 host) to produce the arm64 snap.

## Install

```
snap install --dangerous opengl-cts_<version>_<your_arch>.snap
```

Or from the store, choosing the channel that matches your hardware:

```
snap install opengl-cts                       # default (core24) track
snap install opengl-cts --channel=core22/edge # core22 track
snap install opengl-cts --channel=core26/edge # core26 track
```

The GPU content interface auto-connects for store installs. For a sideloaded
(`--dangerous`) install, connect it manually to match the base:

```
snap connect opengl-cts:graphics-core22 mesa-core22:graphics-core22   # core22
snap connect opengl-cts:gpu-2404 mesa-2404:gpu-2404                   # core24
snap connect opengl-cts:gpu-2604 mesa-2604:gpu-2604                   # core26
```

## Run

To list possible test caselists, run:

```
opengl-cts.list-tests
```

Then run your chosen test(s):

```
opengl-cts.test --deqp-case=KHR-GL46.info.vendor
opengl-cts.test --caselist=data/gl_cts/data/mustpass/gl/khronos_mustpass/4.6.1.x/gl46-main.txt
```

Use `opengl-cts.eglinfo` to inspect the EGL/OpenGL driver stack that will be
used, and `opengl-cts.test --no-confinement` to bypass the GPU content
interface and use the host's EGL/Mesa stack instead.
