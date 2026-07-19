---
title: Nvidia Graphic Card and Visualization Software on WSL2
date: 2026-07-19 23:42:43 +0800
categories: [Technique, OS]
tags: [linux, windows, visualization]
---

In [Cygwin as an Alternative Linux Environment on Windows]({% link 2023-11-05-Cygwin as an Alternative Linux Environment on Windows.md %}), the drawback of WSL2 in visualization was discussed in detail.
An obvious problem with Cygwin is being too old --- a lot of packages are left unmaintained, and it seems that people are gradually embracing WSL instead of Cygwin.

This enables using Nvidia graphic within **WSL2**, so Linux-based visualization software can run smoothly.
Tested on 18 Jul. 2026.

# Install CUDA toolkit

Try this command:

``` bash
$ nvidia-smi
```

If this does not run correctly, CUDA toolkit is required.
As the prerequisite, graphic card driver is need within Windows.
Check [Nvidia user guide](https://docs.nvidia.com/cuda/wsl-user-guide/index.html).

However, the installation guide is not compatible with the latest Ubuntu release.
Use the following commands to add Nvidia source to apt source list :

``` bash
$ wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu-keyring.gpg
$ sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/cuda-wsl-ubuntu-keyring.gpg cuda-wsl-ubuntu-keyring.gpg
$ cd /etc/apt/sources.list.d
$ sudo add-apt-repository 'deb https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/ /'
```

Then update the source and choose the version compatible to the graphic card in the prompt:

``` bash
$ sudo apt modernize-sources
$ sudo apt-get update
$ sudo apt-get install cuda-toolkit-*
```

# Add or check the rendering method

``` bash
$ sudo apt install mesa-utils vulkan-tools
$ glxinfo -B
```

The display port must be 0, i.e., 'name of display: :0'.
Otherwise set the following environmental variable and add it to `~/.bashrc`:

``` bash
$ export DISPLAY=:0
```

If the string below says 'OpenGL renderer string: llvmpipe...', run the following lines and add it to `~/.bashrc`:

``` bash
$ export MESA_D3D12_DEFAULT_ADAPTER_NAME=NVIDIA
$ export GALLIUM_DRIVER=d3d12
```

Then try `glxinfo -B` again.

# Software

## Avogadro 2

Download the [AppImage](https://avogadro.cc/install/index.html#install) version, then run:

``` bash
$ chmod 'ugo+x' Avogadro2-x86_64.AppImage
```

## XCrySDen 1.6.2

Download the [WSL shared version](http://www.xcrysden.org/Download.html) and install the following packages:

``` bash
$ sudo apt install tk libglu1-mesa libtogl2 libxmu6 libgfortran4 openbabel libfftw3-3 \
	imagemagick:amd64=8:6.9.11.60+dfsg-1.3build2
```

**Notes**

1. An old version of imagemagick is required due to conflicts in dependence.
Mirror repositories are probably needed, see the 'Upgrade and maintenance of Ubuntu WSL' section of [About the Windows Subsystem for Linux (WSL)]({% link 2022-01-11-About the Windows Subsystem for Linux (WSL).md %}). 
2. Though it's confirmed that the correct graphic cards is called, the response is still much slower than the Cygwin version for unknown reasons. A compilation is probably required.

## GDIS 
