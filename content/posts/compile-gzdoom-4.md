---
date: 2024-10-07
draft: false
params:
  author: Stradex
title: Compiling GZDoom 4.0.0 in Ubuntu 24.
---


## Compile GZDoom 4.0.0 in Ubuntu 24

In the following post, we will look at how to compile gzdoom engine 4.0.0. The special thing about this version is that
it is the last version compatible for 32 bits OS.

### Requirements

First, let's download the source code for [GZDoom 4.0.0](https://github.com/ZDoom/gzdoom/tree/g4.0.0).

Or just let's clone it like this:

```
mkdir gzdoom
cd gzdoom
git clone --depth 1 --branch g4.0.0 git clone --depth 1 --branch https://github.com/ZDoom/gzdoom.git
```

Now, let's also download zmusic and compile it.

```
mkdir zmusic
cd zmusic
git clone https://github.com/ZDoom/ZMusic.git .
```

### Compile and install ZMusic

```
cd zmusic
mkdir build
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr && make install
```

### Prepare GZDoom 4.0 source code to compile in Linux.

First we need to modify the following files:

```cpp
// file: src/rendering/vulkan/system/vk_device.h

//Add the following header
#include <stdexcept>
```

```cpp
/* files:
src/scripting/types.h
src/utility/earcut.hpp
src/sound/oplsynth/OPL3.cpp
*/      

//Add the following header
#include <limits>
```

Modify CMakelists to local headers are used over system headers to avoid breakage.

[Gist Reference](https://github.com/ZDoom/gzdoom/commit/b95dbaf914618cccaeaa95c650e02be669e477f8)

```
# file: src/CMakeLists.txt
# What I replace will be in comments.

#include_directories( ${GTK3_INCLUDE_DIRS} )
include_directories( SYSTEM ${GTK3_INCLUDE_DIRS} )

#include_directories( ${GTK2_INCLUDE_DIRS} )
include_directories( SYSTEM ${GTK2_INCLUDE_DIRS} )

#include_directories( "${SDL2_INCLUDE_DIR}" )
include_directories( SYSTEM "${SDL2_INCLUDE_DIR}" )

#include_directories( ${OPENAL_INCLUDE_DIR} )
include_directories( SYSTEM ${OPENAL_INCLUDE_DIR} )

#include_directories( "${VPX_INCLUDE_DIR}" )
include_directories( SYSTEM "${VPX_INCLUDE_DIR}" )

#include_directories( "${ZLIB_INCLUDE_DIR}" "${BZIP2_INCLUDE_DIR}" "${LZMA_INCLUDE_DIR}" "${JPEG_INCLUDE_DIR}" "${ZMUSIC_INCLUDE_DIR}" "${DRPC_INCLUDE_DIR}")

include_directories( SYSTEM "${ZLIB_INCLUDE_DIR}" "${BZIP2_INCLUDE_DIR}" "${LZMA_INCLUDE_DIR}" "${JPEG_INCLUDE_DIR}" "${ZMUSIC_INCLUDE_DIR}" "${DRPC_INCLUDE_DIR}")

# include_directories( "${ASMJIT_INCLUDE_DIR}" )
include_directories( SYSTEM "${ASMJIT_INCLUDE_DIR}" )

#include_directories( .

include_directories(
	BEFORE
	.
```

```
# file: src/CMakeLists.txt
# include_directories( "${ZLIB_INCLUDE_DIR}" "${BZIP2_INCLUDE_DIR}" "${LZMA_INCLUDE_DIR}" )
include_directories( SYSTEM "${ZLIB_INCLUDE_DIR}" "${BZIP2_INCLUDE_DIR}" "${LZMA_INCLUDE_DIR}" )
```

### Compile GZDoom engine 4.0.0


```
cd gzdoom
mkdir build
a='' && [ "$(uname -m)" = x86_64 ] && a=64
c="$(lscpu -p | grep -v '#' | sort -u -t , -k 2,4 | wc -l)" ; [ "$c" -eq 0 ] && c=1
cd build/ && f='-UFMOD_LIBRARY -UFMOD_INCLUDE_DIR' && cmake .. -DCMAKE_BUILD_TYPE=Release $f && make -j$c
```
