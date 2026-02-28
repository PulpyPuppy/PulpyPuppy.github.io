---
params:
  author: Temirlan Emilbekov a.k.a. PulpyPuppy
title: RSB Receipt
date: 2026-02-27
description: How I've configured a bset
type: post
---

I’ve finished the initial version of build-set for PortableGL about 3 days ago. It downloads the latest release of PortableGL for today (v0.99.0), executes python script `generate_gl_h.py` which ‘amalgamates’ the final `portablegl.h` header from the sources, and then places it in the include dir.

I’m not sure if running the script to generate the header makes any sense, since the up-to-date (for the current version of  the library) pre-generated header already lies in the root dir of the repository.

On the other hand, some variables can be configured e.g. to include (all of them are included by default though) or exclude several functions.

The `.cfg` invokes python3 as well (obviously) but I’m not sure if it’s a good practice, since I’ve not found any python3 invocations in other `.cfg`’s. Again, on the other hand, RSB uses python itself, so I assume that the host machine has python3 (c’mon which one doesn’t these days?).

Here is the `rsb-root/source-builder/configs/portablegl.cfg`:
```
#
# PortableGL
#
# This configuration file configure's, make's and install's PortableGL.
#

%if %{release} == %{nil}
%define release 1
%endif

Name:      PortableGL-%{portablegl_version}-%{_host}-%{release}
Summary:   PortableGL is an implementation of OpenGL 3.x-ish in C
Version:   %{portablegl_version}
Release:   %{release}
URL:       https://github.com/rswinkle/PortableGL

#
# PortableGL Source
#
%source set PortableGL --rsb-file=PortableGL-%{portablegl_version}.tar.gz https://codeload.github.com/rswinkle/PortableGL/tar.gz/refs/tags/%{portablegl_version}

#
# Prepare the source code.
#
%prep
  build_top=$(pwd)

  source_dir_portablegl="PortableGL-%{portablegl_version}"
  %source setup PortableGL -q -n PortableGL-%{portablegl_version}
  %patch setup PortableGL -p1

  cd ${build_top}

%build
  build_top=$(pwd)

  cd ${build_top}/${source_dir_portablegl}/src
  python3 generate_gl_h.py

  cd ${build_top}

%install
  build_top=$(pwd)

  %{__rmdir} $SB_BUILD_ROOT

  mkdir -p $SB_BUILD_ROOT/%{_includedir}

  cp ${build_top}/${source_dir_portablegl}/src/portablegl.h $SB_BUILD_ROOT/%{_includedir}
```

It didn’t want to build whole the evening, because I didn’t return to `build_top` in `%prep` stage 🥀.

The `.cfg` has `%patch setup PortableGL -p1` line, it has no patches though, as well as the most of other `.cfg`’s. The `patches` dir has just 3 `.diff` files. Does it make any sense to include this line? I guess not. Anyway it doesn’t break anything If I’m not mistaken.

```
$ ../source-builder/sb-set-builder \
--log=log_portablegl_i386 \
--prefix=$HOME/rtems/builds/7 \
--with-rtems-bsp=i386/pc386 \
graphics/portablegl

RTEMS Source Builder - Set Builder, 7 (e01aaf398fb8)
Build Set: graphics/portablegl
config: graphics/portablegl-0.99.0-1.cfg
package: PortableGL-0.99.0-i386-rtems7-1
download: https://dl.rtems.org/dc9d602a3be8be72a93b85a4e1e8b7c940ca65456cc6711a1f5f34f8df7252753afdf3a1dbbdb213d81879e1fb7a8b2aa817522193125d8ebb07badddd2fafb9/PortableGL-0.99.0.tar.gz -> sources/PortableGL-0.99.0.tar.gz
download: https://dl.rtems.org/dc9d602a3be8be72a93b85a4e1e8b7c940ca65456cc6711a1f5f34f8df7252753afdf3a1dbbdb213d81879e1fb7a8b2aa817522193125d8ebb07badddd2fafb9/PortableGL-0.99.0.tar.gz: error: HTTP Error 404: Not Found
download: https://codeload.github.com/rswinkle/PortableGL/tar.gz/refs/tags/0.99.0 -> sources/PortableGL-0.99.0.tar.gz
downloading: sources/PortableGL-0.99.0.tar.gz - 21.7MB
building: PortableGL-0.99.0-i386-rtems7-1
sizes: PortableGL-0.99.0-i386-rtems7-1: 38.899MB (installed: 344.402KB)
cleaning: PortableGL-0.99.0-i386-rtems7-1
reporting: graphics/portablegl-0.99.0-1.cfg -> PortableGL-0.99.0-i386-rtems7-1.txt
reporting: graphics/portablegl-0.99.0-1.cfg -> PortableGL-0.99.0-i386-rtems7-1.xml
staging: PortableGL-0.99.0-i386-rtems7-1 -> /root/rtems/rsb/rtems/build/tmp/sb-0-staging
installing: PortableGL-0.99.0-i386-rtems7-1 -> /root/rtems/builds/7
cleaning: PortableGL-0.99.0-i386-rtems7-1
installing: graphics/portablegl -> /root/rtems/builds/7
clean staging: graphics/portablegl
Staging Size: 347.483KB
Build Set: Time 0:01:00.248167
```

Let’s check:
```
$ ls $HOME/rtems/builds/7/i386-rtems7/pc386/lib/include/portablegl.h && echo yep || echo nope
```

*I was trying to build the portablegl with no BSP built whole the day 🥀*