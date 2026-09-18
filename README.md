# SDL2 SWS video driver

This repository contains the Scarlet SWS video driver for upstream SDL2
2.32.10. It carries only the driver, the SDL integration patch, and a build
recipe; it does not vendor SDL2. Vulkan applications still use the ordinary
Khronos `libvulkan.so.1` loader and the installed ICD. The SWS driver supplies
the fullscreen window, display mode, and input through Scarlet's SWS C client.

The port was exercised with an unmodified Alpine SuperTuxKart 1.5 package on
Scarlet AArch64 (QEMU HVF + VirGL). The game rendered and was playable. The
[Scarlet STK recipe](https://github.com/petitstrawberry/scarlet-bundle-linux/tree/main/producer/recipes/supertuxkart)
records that installation; the SWS C client remains in
[Scarlet](https://github.com/petitstrawberry/Scarlet/tree/feature/vulkan/user/lib/sws-client-c).

Build on Linux AArch64 with CMake, Ninja, a C compiler, `patch`, `curl`, Linux
input headers, and a release `libsws_client_c.so` built for the same target.
From this repository's root:

```sh
make \
  SWS_CLIENT_INCLUDE_DIR=/path/to/Scarlet/user/lib/sws-client-c/include \
  SWS_CLIENT_LIBRARY=/path/to/libsws_client_c.so \
  JOBS=8
```

The Makefile downloads the official SDL2 2.32.10 tarball and checks its SHA-256
before applying `SDL2-2.32.10-sws.patch`. The shared library is
`build/release/libSDL2-2.0.so.0.3200.10`. To install into a staging root, use
the same variables with `make install DESTDIR=/path/to/staging`.

Select the video driver with `SDL_VIDEODRIVER=sws`. The driver currently
requires a Vulkan fullscreen window and uses `VK_KHR_display`; it is not a
general SDL software/OpenGL video backend. Match the SDL library and SWS C
client architecture and ABI with the application.

`SDL_swsvideo.c` is an original MIT-licensed port. SDL2 source and the patch
context retain the upstream SDL zlib license; SDL2 itself is fetched during
the build rather than committed here.
