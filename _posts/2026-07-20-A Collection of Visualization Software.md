---
title: A Collection of Visualization Software
date: 2026-07-20 11:24:02 +0800
categories: [Technique, others]
tags: [visualization, linux]
---

This post collects the visualization software that I found useful.
The recommended installation method, mainly for Linux and WSL, is documented.

**Note for WSL**

Rendering can be very sluggish with the CPU graphic card default for WSL.
Check [Nvidia Graphic Card for WSL2]({% link _posts/2026-07-19-Enable Graphic Card for WSL2.md %}) to enable GPU rendering.

## VESTA

[Link](https://jp-minerals.org/vesta/en/)

Best free crystal visualization tool on Windows --- it sets the standard.
A not comprehensive summary of features includes:

- Atomic structure visualization  
- 3D isosurface plot
- 2D cross-section contour plot
- 1D line profile plot
- Crystal morphology plot
- Miller plane calculation and plot
- Vector plot on atoms
- Geometry format conversion
- Structure generation and modification with space symmetry
- A lot more features available in the manual

### Ubuntu WSL

It is distributed as click-and-run, since it is (as far as I know) not open-source.
Download the binary, check the gtk version (according to the requirements on download page), and install extra dependencies:

``` bash
$ apt list --installed | grep 'gtk'
$ sudo apt install libwebkit2gtk-4.0-37
```

**Note**

AI diagnose identified a compatibility issue of Mesa D3D12 with *WSL apps* base on legacy OpenGL lib.
This means that rendering with GPU would be extremely slow, while CPU remains unaffected.
Use the following command to disable the global setting of rendering with GPU and launch vesta:

``` bash
$ env -u GALLIUM_DRIVER -u MESA_D3D12_DEFAULT_ADAPTER_NAME VMDTITLE=off VMDNOOPTIX=1 /path/to/VESTA
```

## VMD 2.0.0

[Link](https://www.ks.uiuc.edu/Research/vmd/)

Widely used for molecular dynamics.
Periodic boundary is also supported.

Some features compared to VESTA:

- Tcl scripting available, to control the rendering of multiple systems.
- Contour plot on 3D isosurfaces, for molecular electrostatic potential (MEP) analysis, non-covalent interaction (NCI) index, etc.
- Animation rendering

### WSL

Download binary version and unzip the tarball.
`cd` to the unzipped directory and use the following commands:

``` bash
$ sed -i "s|/usr/local|$(pwd)|g" configure
$ ./configure
$ cd src/ | make install
```

**Note**

Similar to VESTA, VMD is also based on legacy OpenGL libs.
Prepend `env -u GALLIUM_DRIVER -u MESA_D3D12_DEFAULT_ADAPTER_NAME VMDTITLE=off VMDNOOPTIX=1` to the command launching VMD.

## Jmol

[Link](https://jmol.sourceforge.net/)

Similar to VESTA, without in symmetry and lattice.

Some features:

- 'click-and-run', better supported than VESTA
- Vibration analysis, including vectors and animation

### WSL

Download the binary version and run `bash jmol.sh`.


## Avogadro 2

[Link](https://avogadro.cc)

A modern visualization tool for non-periodic molecules, with moderate support for crystals.
Some features that stand out compared to VESTA:

- Templates for metallic center, ligands, and functional groups
- Easy manipulation of atom coordinates, bond types etc, good for adsorption modelling
- Interface to non-periodic DFT and MD codes
- Interface to [OpenBabel]{https://openbabel.org/index.html} for initial geometry relaxation and conformer search, based on classical force field

### WSL

Download the [AppImage version](https://avogadro.cc/install/index.html#install), then run:

``` bash
$ chmod 'ugo+x' Avogadro2-x86_64.AppImage
```

## XCrySDen 1.6.2

[Link](http://www.xcrysden.org/XCrySDen.html)

A visualization tool for crystals, good for specific tasks when needed.
However, it is not well-updated and might cause conflicts in package dependence.

Some features compared to VESTA:

- More intuitive 2D cross-section contour plot, which includes both the heat map and the atomic structure (it is also possible to do that in VESTA, but needs to check the manual)
- Fermi surface visualization in reciprocal space (i.e. 3D isosurface for band structure)
- 1st Brillouin zone and k path visualization
- Animation rendering 
- Interface to CRYSTAL, Quantum Espresso, and Wien2k


### Ubuntu WSL

Download the [WSL shared version](http://www.xcrysden.org/Download.html) and install the following packages:

``` bash
$ sudo apt install tk libglu1-mesa libtogl2 libxmu6 libgfortran4 libfftw3-bin \
	openbabel imagemagick
```

**Notes**

1. The 'fftw3-3' package in official tutorial is deprecated and can lead to conflicts in dependence. The new 'fftw3-bin' package is used.
2. Similar to VESTA, XCrySDen is also based on legacy OpenGL libs.
Prepend `env -u GALLIUM_DRIVER -u MESA_D3D12_DEFAULT_ADAPTER_NAME VMDTITLE=off VMDNOOPTIX=1` to the command launching XCrySDen.

### Cygwin

Download the [Cygwin shared version](http://www.xcrysden.org/Download.html) and install the packages specified in the tutorial via installer.
To setup Xming forwarding, check [Cygwin as an Alternative Linux Environment on Windows]({% link _posts/2023-11-05-Cygwin as an Alternative Linux Environment on Windows.md %}).

### Source code compilation

For shared library, download [BWideget 1.9.13](https://sourceforge.net/projects/tcllib/files/BWidget/1.9.13/) and unzip the tarball as `<source code directory>/external/lib/bwidget-1.9.13`.

Then in Ubuntu, install:

``` bash
$ sudo apt install tk libglu1-mesa libtogl2 libxmu6 libgfortran4 libfftw3-bin \
	openbabel imagemagick
$ sudo apt install libx11-dev libxmu-dev tcl8.6-dev tk8.6-dev mesa-common-dev \
	libglu1-mesa-dev libtogl-dev libfftw3-dev
$ cd <source code directory>
$ cp system/Make.sys-shared Make.sys
```

In 'Make.sys', insert `-fcommon` before `-pedantic -Wall` for `CPPFLAGS` to address a new default of GCC compiler that may cause error in compilation.

Then run *serial* compilation `make -j1 all`.


## GDIS 

[Link](https://github.com/arohl/gdis)

Similar to XCrySDen, good for specific tasks but not up-to-date.
Features compared to VESTA:

- Better slab modelling for molecular crystals, which generates wrinkled surfaces to preserve the integrity of molecules intersecting with the Miller plane
- Interface to GULP, allowing calculations for crystals based on classical force field (symmetry in lammps sucks)
- Interface to VASP

### Ubuntu WSL Compilation

Install the following packages and download the GitHub repository:

``` bash
$ sudo apt install libgtk2.0-bin libgtk2.0-common libgtk2.0-0t64 libgtk2.0-dev \
	libgtkglext1 libgtkglext1-dev
$ git clone https://github.com/arohl/gdis.git
```

In 'src/makefile.linux', insert the `-std=gnu17` flag as the first flag for `CFLAGS`.
Then run `./install` and follow the instructions.
Use `chmod 'ugo+x' gdis` to add permissions.

### Cygwin Compilation

Install gtk+2 and gtkglext packages with Cygwin installer.
Then follow the same procedures as the previous section.
