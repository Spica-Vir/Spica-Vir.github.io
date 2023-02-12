---
title: LAMMPS compilation and integration with Python
date: 2022-12-30 15:32:20 +0000
categories: [Technique, Parallel Computing]
tags: [linux, LAMMPS]
---

Procedures to Compile [LAMMPS](https://www.lammps.org/#gsc.tab=0) as both static and dynamic libs are summarized in this post. When compiled as the dynamic lib, the integration of LAMMPS and python environment is briefly explored. The compiled version of LAMMPS is 23Jun2022, with OpenMP threading, GPU acceleration and FFT by FFTW3. Tested on NHPC101 (OpenSUSE Leap 15.3), from Dec. 28 to Dec. 30 2022. 

# Prerequisites

Environment establishment:

- cmake 3.23.1  
- fftw 3.3.10  
- Intel OneAPI 2022.1.2.146
- NvidiaHPC-SDK 22.3 (CUDA 11.6)

Basic information about NHPC101:

- CPU: Intel core i7-7700  
- GPU: Nvidia Quadro P400

## cmake

The compilation of [cmake](https://cmake.org/) is rather clear, which is also given in [LAMMPS manual](https://docs.lammps.org/Build_cmake.html#installing-cmake). All the compilations are based on cmake.

## FFTW3

**Trivial molecular dynamics calculations by LAMMPS does not require FFT**. The [KSPACE package](https://docs.lammps.org/Packages_details.html#pkg-kspace), and probably other packages involving reciprocal space, requires FFT to solve long-range interactions. It is also pointed out in the document that, without FFTW3, LAMMPS can also build FFT libs by the built-in KISS package. FFTW3 can probably improve the performance but for sure brings more troubles in compilation. See [this page](https://docs.lammps.org/Build_settings.html#fft-library). 

The compilation procedures of FFTW3 libs are given in [Optimized Compilation of FFTW3](https://spica-vir.github.io/posts/Optimized-Compilation-of-FFTW3/). The built-in FFTW wrapper of Intel OneAPI is not sufficient to meet the needs. As suggested in the [manual](https://docs.lammps.org/Build_settings.html#fft-library), a single-precision FFTW3 lib is used.

## Intel Suite

For Intel CPUs, the Intel suite are helpful to get the optimal accelerations especially when combined with the [INTEL package](https://docs.lammps.org/Build_extras.html#intel). This package also offers moderate acceleration when the code is compiled with other compilers. It should be noted that the current implementation of LAMMPS supports classic C/C++ compilers only, i.e., `CC=icc` and `CXX=icpc`. The DPC++/C++ compiler (`icx`) leads to errors. 

## GPU acceleration

Nvidia compilers are not required, so the NvidiaHPC-SDK suite is not necessarily needed, but CUDA is required. The [GPU package](https://docs.lammps.org/Build_extras.html#gpu-package) is a relatively independent one, which can be skipped without interfering the integrity of the code. To include GPU acceleration, the following paths must be added before compilation if the CUDA is not loaded individually by environment modulus, and it should be noted that NvidiaHPC-SDK suite should not be loaded as a whole since the Intel suite is needed:

``` console
~$ export PATH="$PATH:/path/to/NvidiaHPC-SDK/22.3/cuda/11.6/bin"
~$ export LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:/path/to/NvidiaHPC-SDK/22.3/cuda/11.6/lib64"
~$ export LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:/path/to/NvidiaHPC-SDK/22.3/cuda/11.6/extras/CUPTI/lib64"
```

# Compilation

LAMMPS can be compiled either statically or dynamically. Both can produce executable. The previous option is similar to the traditional computational codes, which executes jobs and give results as a blackbox and can be called by an external application. The latter one can be loaded as a dynamic lib (which is much easier than being loaded as a static lib) and enables interactive developing, such as being integrated with atomic simulation environment (ASE). But dynamic linking requires more settings as the compilation does not really link the required libs but records their paths only. 

## Static build

The following options enable a static linking:

``` console
~$ cmake ../cmake --install-prefix /path/to/lammps/23Jun2022/ -D LAMMPS_MACHINE=stc -D CMAKE_CXX_COMPILER=icpc -D PKG_INTEL=ON -D INTEL_ARCH=cpu -D PKG_KSPACE=ON -D PKG_MOLECULE=ON -D PKG_EXTRA_MOLECULE=ON -D PKG_EXTRA_PAIR=ON -D PKG_PHONON=ON -D PKG_MANYBODY=ON -D BUILD_OMP=yes -D PKG_OPENMP=ON -D CMAKE_CXX_FLAGS=-fopenmp -D CMAKE_EXE_LINKER_FLAGS=-fopenmp -D PKG_GPU=ON -D GPU_API=cuda -D GPU_PREC=double -D GPU_ARCH=sm_60 -D FFT=FFTW3 -D FFT_SINGLE=YES -D FFTW3F_LIBRARY=/path/to/fftw/3.3.10/lib64/libfftw3f.a -D FFTW3F_OMP_LIBRARY=/path/to/fftw/3.3.10/lib64/libfftw3f_omp.a
~$ cmake --build .
~$ make install
```

Packages are loaded according to needs. Some comments:

1. As stated before, `CMAKE_CXX_COMPILER=icx` (the new Intel DPC++/C++ compiler) can lead to errors.  
2. EXTRA_MOLECULE and EXTRA_PAIR packages are automatically removed during configuration. Edit 'CMakeCache.txt' afterwards to reopen them.  
3. GPU-related options depend on architecture. See the [LAMMPS manual](https://docs.lammps.org/Build_extras.html#gpu-package) for instructions.  
4. When the single-precision FFTW3 lib is used (`FFT_SINGLE=YES`), the keywords of libs should be `FFTW3F_LIBRARY` and `FFTW3F_OMP_LIBRARY`. Otherwise, they are `FFTW3_LIBRARY` and `FFTW3_OMP_LIBRARY`. Static libs are strongly recommended for static linking.  
5. The code can auto decide whether to launch threaded FFTW3 compilation according to `FFTW3_OMP_LIBRARY`. Using `FFT_FFTW_THREADS` is redundant and can lead to an error that the FFTW3 lib supporting OpenMP is not found (even though `FFTW3_OMP_LIBRARY` is specified).

## Dynamic build

The following options enable a dynamic linking:

``` console
~$ cmake ../cmake --install-prefix /path/to/lammps/23Jun2022/ -D LAMMPS_MACHINE=dym -D CMAKE_CXX_COMPILER=icpc -D PKG_INTEL=ON -D INTEL_ARCH=cpu -D PKG_KSPACE=ON -D PKG_MOLECULE=ON -D PKG_EXTRA_MOLECULE=ON -D PKG_EXTRA_PAIR=ON -D PKG_PHONON=ON -D PKG_MANYBODY=ON -D BUILD_OMP=yes -D PKG_OPENMP=ON -D CMAKE_CXX_FLAGS=-fopenmp -D CMAKE_EXE_LINKER_FLAGS=-fopenmp -D BUILD_SHARED_LIBS=ON -D LAMMPS_EXCEPTIONS=ON -D PKG_PYTHON=ON -D PKG_GPU=ON -D GPU_API=cuda -D GPU_PREC=double -D GPU_ARCH=sm_60 -D FFT=FFTW3 -D FFT_SINGLE=YES -D FFTW3F_LIBRARY=/path/to/fftw/3.3.10/lib64/libfftw3f.so -D FFTW3F_OMP_LIBRARY=/path/to/fftw/3.3.10/lib64/libfftw3f_omp.so
~$ cmake --build .
~$ make install
```

Some comments:

1. A dynamic build is launched by `BUILD_SHARED_LIBS=ON`.   
2. The PYTHON package is included for integrating with python developing environment, see [LAMMPS manual](https://docs.lammps.org/Python_head.html).  
3. The `LAMMPS_EXCEPTIONS=ON` option is to help keep and trace back the potential LAMMPS errors happened within python scripts.  
4. Dynamic FFTW3 libs ('libfftw3f.so', 'libfftw3f_omp.so') are used in this example.

# Integration with Python developing environment

## Use static libs

Loading the static builds for simulations and other packages for pre- and post- processing is a rather mature technique, which involves mainly python programming and a moderate amount of Linux command line. This can be achieved by already developed packages such as [ASE LAMMPS calculators](https://wiki.fysik.dtu.dk/ase/ase/calculators/lammps.html#module-ase.calculators.lammps) and [PyIron](https://pyiron.readthedocs.io/en/latest/source/notebooks/first_steps.html#Perform-a-LAMMPS-MD-simulation).

## Enable a object-oriented programming

An purely object-oriented programming fashion is realized by loading the dynamic lib to Python developing environment, see [LAMMPS manual](https://docs.lammps.org/Python_head.html). The following paths should be specified:

- the dynamic lib file, in '/path/to/lammps/23Jun2022/lib64/liblammps_dym.so'  
- the LAMMPS python module, in '/path/to/lammps/23Jun2022/lib/site-packages/lammps' 

The default lib name for lammps object is 'liblammps.so'. To correctly import and create the lammps object, the name can be specified by `lammps.lammps(name='dym')`, or use the following command:

``` console
~$ ln -s /path/to/lammps/23Jun2022/lib64/liblammps_dym.so /path/to/lammps/23Jun2022/lib64/liblammps.so
```

To correctly load the LAMMPS python module, the user can either merge the 'lib/site-package' folder with the default 'site-package' directory of the conda environment, or export the path to the environmental variable `$PYTHONPATH`. Alternatively, the path can be added on the top of python scripts, which is recommended:

``` python
>>> import sys
>>> sys.path.append('/path/to/lammps/23Jun2022/lib/python3.X/site-packages')
>>> import lammps
```

The variable `${OMP_NUM_THREADS}` can be set if multi-threading is activated in compilation. The default vale is 1, i.e., no shared memory threading:

``` console
~$ export OMP_NUM_THREADS=2
```

Launch the python environment, use the following commands:

``` python
>>> import lammps
>>> lmp=lammps.lammps()
LAMMPS (23 Jun 2022)
  using 2 OpenMP thread(s) per MPI task
>>> exit()
```

## A collection of potential problems

When loading the dynamic lib, it is probable that an error occurs: **OSError: libpython3.10.so.1.0: cannot open shared object file: No such file or directory**. Executable linked dynamically does not include the basic python libs, so the path to python libs should be added to `${LD_LIBRARY_PATH}`. Auto edition of environmental variables can be realized during activation / deactivation of anaconda environments. Use the following commands to set initialization options and then restart the python environment:

``` console
~$ mkdir -p ${CONDA_PREFIX}/etc/conda/activate.d
~$ touch ${CONDA_PREFIX}/etc/conda/activate.d/env_vars.sh
~$ echo "export LD_LIBRARY_PATH=\"\${CONDA_PREFIX}/lib:\${LD_LIBRARY_PATH}\"" >> ${CONDA_PREFIX}/etc/conda/activate.d/env_vars.sh
~$ mkdir -p ${CONDA_PREFIX}/etc/conda/deactivate.d
~$ touch ${CONDA_PREFIX}/etc/conda/deactivate.d/env_vars.sh
~$ echo "LD_LIBRARY_PATH=\`echo \${LD_LIBRARY_PATH//\"\${CONDA_PREFIX}/lib:\"/''}\`" >> ${CONDA_PREFIX}/etc/conda/deactivate.d/env_vars.sh
```

By doing so, the *ncurses* lib from Anaconda seems to cover the default one, which leads to 2 problems. The first one is warnings such as '**libtinfo.so.6: no version information available message using conda environment**', which is due to the releases of ncurses on the default Anaconda channel lack the version information. This can be solved by reinstalling it from the `conda-forge` channel:

``` console
~$ conda install -c conda-forge ncurses
```

The second one is that it makes the `top` command, and probably more commands, invalid. But the `ps` command is effective. If the error occurs, finding an alternative or deactivating the current environment seem to be the only 2 solutions. 

If the dumping option of video is activated, the path to the *ffmpeg* (for dumping videos) executable should be exported to `${PATH}`, while *libpng*, *zlib* and *libjpeg* remain accessible.