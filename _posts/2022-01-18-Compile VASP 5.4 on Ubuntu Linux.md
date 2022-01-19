---
title: Compile VASP 5.4.1 on Ubuntu/Debain Linux
date: 2022-01-18 17:07:32 +0000
categories: [Technique, Parallel Computing]
tags: [software, VASP]
---

This post is to briefly introduce available methods to compile VASP 5.4 on Ubuntu or Debain OSs. Tested on Debian WSL2, Windows 10 21H2, Jan 9-21. VASP edition 5.4.1.05Feb16. 

Currently there are two mainstream compiling and parallelizing solutions for VASP: by Intel parallel studio + Ifortran, or by openmpi + gfortran. The former one is an almost 'all-in-one' solution and easier to compile, but a valid license number is needed, which can be requested free of charge via educational/academic email addresses. The latter one is based on GNU license and free of charge, but more difficult - libraries of gfortran, openmpi, and ScaLAPACK are needed and package dependency should be carefully checked. According to many online tests, if parameters in the 'makefile.include' file (see below) are set properly, there is only a negligible difference in performances of VASP running on Intel package or GNU packages. 

P.S. The author do not actually use VASP for research since July 2020, so the information provided here may not be up-to-date and well-maintained. Anyway, it is easy to get instructions from [forums](https://www.vasp.at/forum/viewforum.php?f=6) and [Wiki](https://www.vasp.at/wiki/index.php/The_VASP_Manual#Getting_started). 

# By Intel
References  
* [A text, step-by-step guide on Ubuntu](http://bbs.keinsci.com/thread-14470-1-1.html), in simplified Chinese.  
* [A video guide on CentOS](https://www.bilibili.com/video/av39616222/), in Chinese.  

## A recent issue with Intel parallel studio
The latest available edition of Intel parallel studio is [XE 2020](https://www.intel.com/content/www/us/en/developer/articles/release-notes/intel-parallel-studio-xe-2020-composer-edition-fortran-debug-solutions-release-notes.html). Releases after that have been integrated into [oneAPI](https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html#gs.mcqdw6) and no new license number is released for the previous editions. Fortunately, oneAPI can be requested free of charge without a student license, and its usage is generally the same as the parallel studio. The environment used here is Parallel Studio XE 2020, which can still be obtained via 'non-official' sources.

## Prerequisites
For a newly installed WSL2 Debain subsystem, many packages are missing, Packages listed below should be installed via `sudo apt-get install` at least before installing Intel parallel studio: 

* `libgtk-3*`  
* `libxss1*`  
* `libnss3`  
* `libgtk2.*common`  
* `libpango-1*`  
* `libasound2*`  
* `xerver-xorg`  

Besides, packages listed below should be installed at least before compiling VASP:

* `rsync`  
* `gcc`  

## Install Intel parallel studio
1. Unzip the package with the `tar -zxvf` command and enter the folder.  
2. Type the command `./Install_GUI.sh` and enter the installing environment. Follow the instructions to complete the installation.  
3. Set the environment variable for the Ifort compiler and the MKL library in `~/.bashrc`. Substitute `/your-intel-directory/` with the actual directory you place the folder.   
``` console 
> echo "source /your-intel-directory/intel/bin/compilervars.sh intel64" >> ~/.bashrc
> echo "source /your-intel-directory/intel/mkl/bin/mklvars.sh intel64" >> ~/.bashrc
> source ~/.bashrc
```

4. Check whether the compiler is installed and set up successfully with the commands `ifort -v` and `echo $MKLROOT`. 

## Compile VASP



# By GNU