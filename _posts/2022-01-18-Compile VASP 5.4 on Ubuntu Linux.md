---
title: Compile VASP 5.4.1 on Ubuntu/Debain Linux
date: 2022-01-18 17:07:32 +0000
categories: [Technique, Parallel Computing]
tags: [software, VASP, linux]
---

This post is to briefly introduce available methods to compile VASP 5.4 on Ubuntu or Debain OSs. Tested on Debian WSL2, Windows 10 21H2, Jan. 9-21. VASP edition 5.4.1.05Feb16. 

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

4. Check whether the compiler has been successfully installed and set up successfully with the commands `ifort -v`, ``icc -v`` and `echo $MKLROOT`.  

## Compile Intel fftw3 (Fast Fourier transformation)
Enter the folder `intel/mkl/interfaces/fftw3xf` and type the command `make libintel64` to compile Intel fftw3. The output is `libfftw3xf_intel.a`.  

## Patches
See below: By GNU/Get patches

## Compile VASP
1. Unzip the VASP package and enter the folder.  
2. Copy the file `arch/makefile.include.linux_intel` to the current directory, and rename it as `makefile.include`.  
3. Open `makefile.include`, find the line with `OFLAG` and substitute it with the line below:  
``` 
OFLAG      = -O2 -xhost
```

This command improves the performance of the compiled VASP.  

4. In the same directory, enter the command `make all` and wait.  

## Usage
The compiled program is at the `bin/` sub-folder. The most commonly used edition is `vasp_std`. 

To launch VASP, firstly load Intel mpi and then execute `vasp_std` in parallel:   
``` console
> source /your-intel-directory/intel/parallel_studio_xe_2020/psxevars.sh
> mpirun -np [number of processors] VASP/directory/bin/vasp_std > printout.log 
```

# By GNU
Compilation with GNU packages are more cumbersome because of the absence of a one-stop solution. Various packages and libraries are needed, and their dependencies and proper parameters should be checked carefully. The steps below were tested reliable in Jan. 2020 on a WSL1 Debain subsystem. However, error was reported when compiling the same source codes in Jan. 2022, saying that inconsistency in data type was detected for a certain variable. Not sure whether it originates from a library assumed to be unnecessary (libscalapack-openmpi1, see below). This section is listed here as a supplement. 

Reference:  
* [A text guide](https://www5.hp-ez.com/hp/calculations/page377) to compile VASP5.4.1 with GNU software, in Japanese.  
* [Another text guide](https://ishxiao.com/blog/ubuntu/2017/10/15/how-to-compile-install-vasp-5.4.1.html) in English.  

## Prerequisites
In practise the packages in both references are installed before VASP is successfully compiled. Therefore many packages are included (and some of them might be redundant): 

**Compilers**

* `csh`  
* `g++`  
* `gfortran`  
* `g++build-essential`  

**MPI**

* `libopenmpi-dev`  

**Linear Algebra & Parallelization**

* `liblapack-dev`  
* `libblas-dev`  
* `libtmglib-dev`  
* `libatlas-base-dev`  
* `libscalapack-mpi-dev`  
* `libscalapack-openmpi1`  
* `libscalapack-openmpi-dev`  

`libscalapack-openmpi1` is an old-fashioned package and not available via `apt-get install`. An alternative is downloading it from [the Debain webpage](https://packages.debian.org/stretch/libscalapack-openmpi1) manually checking its dependency. It depends on packages below: 

* `gcc-6-base`  
* `libgofortran3`  
* `libopenmpi2(old stable)`  
* `libblacs-openmpi1`  

These packages are also recommended to be installed manually because of the version problem.  

**Fast Fourier transformation**

* `libfftw3-dev`
* `libfftw3-3`

**Synchronizing data**

* `rsync`

## Get patches
Unzip the VASP packages and enter the folder. Use the commands below to add patches to the source code: 

``` console
> wget http://cms.mpi.univie.ac.at/patches/patch.5.4.1.14032016.gz
> wget http://cms.mpi.univie.ac.at/patches/patch.5.4.1.03082016.gz
> gunzip patch.5.4.1.14032016.gz
> gunzip patch.5.4.1.03082016.gz
> patch -p0 < patch.5.4.1.14032016
> patch -p0 < patch.5.4.1.03082016
```

Issue identified Jan. 9-21  
: Unfortunately due to the abandon of the old website, both links are invalidate. The [VASP Wiki](https://www.vasp.at/wiki/index.php/Installing_VASP.5.X.X#For_vasp.5.4.1.05Feb16_.28with_GPU_support.29) did not provide any updated resources for this issue, which influences all the patches for VASP5. Therefore, please check the descriptions of both patches and consider your propose of running VASP before continuing. 

## Compile VASP
1. Copy the file `arch/makefile.include.linux_gfortran` to the current directory, and rename it as `makefile.include`. 
2. Modify the file as follows. Check the directories in variables `LIBDIR`, `OBJECTS`, and `INCS`：

``` 
# Precompiler options
CPP_OPTIONS= -DMPI -DHOST=\"IFC91_ompi\" -DIFC \
             -DCACHE_SIZE=4000 -Davoidalloc \
             -DMPI_BLOCK=8000 -DscaLAPACK -Duse_collective \
             -Duse_bse_te -Duse_shmem -Dtbdyn

CPP        = gcc -E -P -C $*$(FUFFIX) >$*$(SUFFIX) $(CPP_OPTIONS)

FC         = mpif90.openmpi
FCL        = mpif90.openmpi

FREE       = -ffree-form -ffree-line-length-none

FFLAGS     = 
OFLAG      = -O2 -mtune=native -m64
OFLAG_IN   = $(OFLAG)
DEBUG      = -O0

LIBDIR     = /usr/lib/x86_64-linux-gnu
BLAS       = -L$(LIBDIR) -lblas
LAPACK     = -L$(LIBDIR) -ltmglib -llapack
BLACS      = -lblacs-openmpi -lblacsCinit-openmpi -lblacsF77init-openmpi
SCALAPACK  = -L$(LIBDIR) -lscalapack-openmpi $(BLACS)

OBJECTS    = fftmpiw.o fftmpi_map.o  fftw3d.o  fft3dlib.o \
             /usr/lib/x86_64-linux-gnu/libfftw3.a
INCS       =-I/usr/include -I/usr/lib/openmpi/include

LLIBS      = $(SCALAPACK) $(LAPACK) $(BLAS)

OBJECTS_O1 += fft3dfurth.o fftw3d.o fftmpi.o fftmpiw.o chi.o
OBJECTS_O2 += fft3dlib.o

# For what used to be vasp.5.lib
CPP_LIB    = $(CPP)
FC_LIB     = $(FC) 
CC_LIB     = gcc
CFLAGS_LIB = -O
FFLAGS_LIB = -O1
FREE_LIB   = $(FREE)

OBJECTS_LIB= linpack_double.o getshmem.o

# Normally no need to change this
SRCDIR     = ../../src
BINDIR     = ../../bin
```

3. `make all`

## Usage
Similar to VASP compiled by Intel parallel studio, the command to launch parallel computation is: 
``` console
> /usr/bin/mpirun.openmpi -np [number of processors] VASP/directory/bin/vasp_std > printout.log
```
