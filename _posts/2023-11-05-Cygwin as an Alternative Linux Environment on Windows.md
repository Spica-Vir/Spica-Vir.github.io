---
title: Cygwin as an Alternative Linux Environment on Windows
date: 2023-11-05 23:32:03 +0000
categories: [Technique, OS, Research, DFT]
tags: [linux, windows]
---

# Stories about Windows+Linux

It was late 2019 and early 2020 when I was firstly exposed to Linux system and began to learn how an OS works. I am not saying that I 'learned' it since it is a lifelong learning process. [VMware Player](https://www.vmware.com/uk/products/workstation-player.html) was the first virtual machine platform that I learned to use, thanks to the molecular dynamics lecture delivered by [Prof. W. Qi (齐卫宏 教授)](https://scholar.google.com/citations?hl=en&user=oOMluDMAAAAJ&view_op=list_works&sortby=pubdate) at NWPU - by the way he gave away textbooks by him for free, which I keep till today as an interesting souvenir and reference. It (the software!) was not good for many reasons, frankly speaking. It was slow and unstable due to not running on the actual hardware, and to transfer files a plugin was needed to load the disk in 'real' OS as a virtual optical drive. The good thing was that virtual OS was not limited by the actual hardware used, so Android, iOS etc. developers can test codes on a x86 device, but that hardly mattered to me. The popular practice among Computation Department students was enabling the dual OS (Linux + Windows) option. That completely isolates 2 OSs and only the hardware is shared, bringing troubles of file transfer. 

Meanwhile, or even earlier, the first generation of [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/compare-versions) was released. It was a real game changer, as it was smoothly integrated into the file system and did not even rely on a virtual machine platform. I am not pretending to be a computer scientist, but WSL1 seems to run Linux executable just as smoothly as Windows executable. I compiled and ran VASP on my laptop for several weeks in early 2020's lockdown. That strategy brought many troubles for future developing and unfortunately Microsoft is always the opposite of open-source. WSL1 was quickly replaced by WSL2, which is a light-weighted virtual machine sharing the file system with Windows. The performance was reduced as the compromise for a fully functioning Linux environment with graphic user interface (GUI). I am generally happy with that since indeed there are some complex and even ridiculous issues with WSL1 and the performance, in terms of CPU and disk, is not that badly deteriorated. The noticeable trouble is GUI, and here is the dilemma: WSL1 - better performance but no GUI; WSL2 - reduced performance but at least has GUI. This becomes particularly annoying when there is a proper graphic card on the laptop but for sure it is just idling around while CPU is groaning.

Windows seems to be extremely unpopular for atomic modelling, except for the really brilliant [VESTA](https://jp-minerals.org/vesta/en/download.html) and others, the option seem to be Materials Studio, a good example of how science is capitalized and monopolized. Making GNU and FOSS visualization tools available to Windows is therefore important. [Cygwin](https://www.cygwin.com/) is a less well-known option compared to those mentioned above but it solves my GPU troubles recently.

# Cygwin
## Cygwin vs WSL
The fundamental difference between Cygwin and WSL is that Cygwin provides libraries during compilation in such a way that Linux codes are 'wrapped' and the compiled executable is a windows executable dynamically linked to Cygwin libs, while WSL is a Linux virtual machine that generates Linux executable. Because of this, Cygwin executable has limited portability and availability (not all Linux executable support Cygwin), which typically runs only in Cygwin environment (not even Windows) unless dependencies are correctly sorted out in Windows environment. But as it is, in principle, a windows exe file, so it runs in the same way as other installed software. WSL compiles executable for all Linux machines as long as the dependency, OS distribution, hardware etc. are consistent. It also has no extra requirement on software, which means if a code runs on a similar Linux machine, it also runs on WSL. Its drawbacks, as discussed in the previous section, is failing to utilize hardware.

In terms of file management, the root directories of both can be visited via file manager. WSL can visit windows disk in `/mnt`, but Cygwin cannot visit files in the main OS, so files have tobe copied into Cygwin installation directory for its terminal to visit. 

## Package management
The installation tool is also the package management tool for Cygwin, i.e., `apt` or `yum` for other Linux distributions. The root directory is Cygwin installation directory. Here is a screenshot of Cygwin package manager user interface. 'Skip' means not-yet-installed packages.

![Cygwin Package Manager](/large/0086k6b8gy1i8u52hmumtj30ub0ig44b.jpg){: .centered; w="720"}

## XMing and Graphics
The graphic user interface (GUI) can be enabled following [Microsoft's Tutorial](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps), though my personal attempts in installing a GUI desktop always ended up in failure. As far as I am aware of, Cygwin requires X Window System (My supervisor called it an old school way but I don't agree with him) to run GUI apps. [Xming](http://www.straightrunning.com/XmingNotes/) is a free, open source package for Windows. For Cygwin, X11 components are needed. In package manager, search for 'xclock' to add a very basic X11 application and all dependencies to Cygwin environment. 

After finishing installations, to run GUI apps, Xming should be launched as an idling server. The screenshot below shows the default option. GUI apps in Cygwin are launched in separate windows. Other options are for X Windows based desktop applications which displays the Linux environment as a proper graphic OS. 

![Xming Setups](/large/0086k6b8gy1i8u53pw0kej30yj0qy10d.jpg){: .centered; w="640"}

Then use the default settings. Since the display number is '0' for Xming server, in Cygwin, use `export DISPLAY=:0` to set the display port for client.

Run `xclock`.

![Xclock](/large/0086k6b8gy1i8u54m4n8ej315r0p11e1.jpg){: .centered; w="640"}

# Programs on Cygwin
## XCrySDen
I really like XCrySDen as a free visualization tool for atomic structures and electron properties. The compiled distribution and instructions are available [online](http://www.xcrysden.org/Download.html).

## GDIS
[GDIS](https://github.com/arohl/gdis) is a free, open source visualization tool developed in Curtin, West Australia, which does a neat symmetry analysis job especially for complex molecular crystals, as it was initially developed for classical force field people. As a DFT guy, I am beaten hard by them every fortnight. It requires `libgtk-2.0` and `libgtkglext` and their '-devel' distributions as compiling GDIS requires `pkg-config`.

## CRYSTAL
It might be too much for [a DFT code](https://www.crystal.unito.it/) on PC. It was intended for use with XCrySDen to read and visualize CRYSTAL wavefunction files. There is nothing new for compilation, which is actually easier as all the needed libraries are installed as the root user.

However, for some reason the serial and static CRYSTAL does not run properly with XCrySDen (it does individually). Pre-compiled XCrySDen does not work well with wavefunction interpretation but fine with input files. Locally compiled XCrySDen work with neither.

## VMD and DL Visualize
Neither of them works as they have platform checks. There should be a way to walk around but currently I don't bother to check that as I have a genuine Linux machine at lab :) 
