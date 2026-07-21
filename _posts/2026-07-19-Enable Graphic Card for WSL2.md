---
title: Nvidia Graphic Card for WSL2
date: 2026-07-19 23:42:43 +0800
categories: [Technique, OS]
tags: [linux, windows, visualization]
---

In [Cygwin as an Alternative Linux Environment on Windows]({% link 2023-11-05-Cygwin as an Alternative Linux Environment on Windows.md %}), the drawback of WSL2 in visualization was discussed in detail.
An obvious problem with Cygwin is being too old --- a lot of packages are left unmaintained, and it seems that people are gradually embracing WSL instead of Cygwin.

This enables using Nvidia graphic within **WSL2**, so Linux-based visualization software can run smoothly.
Tested on 18 Jul. 2026.

## Install CUDA toolkit

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

## For visualization and graphic rendering

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
It should give something like 'OpenGL renderer string: D3D12 (NVIDIA RTX A1000 Laptop GPU)'.
