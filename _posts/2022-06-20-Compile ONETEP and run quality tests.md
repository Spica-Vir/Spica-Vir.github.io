---
title: Compile ONETEP and run quality tests
date: 2022-06-20 17:34:38 +0100
categories: [Technique, Parallel Computing]
tags: [ONETEP, linux]
---
This post summarizes how to successfully compile ONETEP and run quality tests. The available version of ONETEP is v6.1.9, released on Jun. 2, 2022. Used compilers + fftw + libraries include:

1. For [Imperial CX1](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/) and lab server(OpenSUSE Leap 15.3): [Intel OneAPI](https://www.intel.cn/content/www/cn/zh/developer/tools/oneapi/toolkits.html#gs.45znw6) tool kit 2022.1.2 versions - ifort 2022, FFTW3 as the wrapper of Intel MKL, ScalAPACK, OpenMP.  
2. For [ARCHER2](https://docs.archer2.ac.uk/): Cray versions of Fortran compiler v12.0.3, mpich v8.1.4 and fftw3 v3.8.11. With OpenMP and ScaLAPACK.  

Tested from Jun. 19 to Jun. 20, 2022.

# General introductions

[ONETEP](https://onetep.org/), the Order-N Electronic Total Energy Package, is a linear-scaling periodic DFT package based on orthogonal generalized Wannier function basis sets. Its basis set is equivalent to planar-wave BSs so is free from the famous basis set superposition error(BSSE) and has tunable completeness by cut-off energy. Meanwhile, it also has attributes of atomic orbitals like relative small basis sets and therefore suitable for large system. Its most important feature is that its computational load strictly linear-scales with the system size.

# Compile ONETEP on ARCHER2

Compiling ONETEP on supercomputers is relatively easy thanks to the sensible developers and cluster maintenance team. Detailed institutions can be found in the folder 'hpc_resources/'. The pre-compiled version is available as an environmental module on ARCHER2, to which the access can be requested via your [ARCHER2 account at SAFE](https://safe.epcc.ed.ac.uk/). No modification is recommended unless you are an expert user (in which case you won't refer to this page).

# Compile ONETEP with Intel OneAPI
## Multi-process parallelization

Get Intel OneAPI 2022.1.2.184 base version and HPC version:

``` console
$ wget https://registrationcenter-download.intel.com/akdlm/irc_nas/18487/l_BaseKit_p_2022.1.2.146_offline.sh
$ wget https://registrationcenter-download.intel.com/akdlm/irc_nas/18479/l_HPCKit_p_2022.1.2.117_offline.sh
``` 

According to the recommendation in the file 'INSTALL', the threaded FFTW3 wrapper of MKL is recommended (-DMKL_FFTW3) instead of a standalone FFTW3 lib to make sure the compiled version is **thread-safe** (Of course a standalone FFTW3 is fine if ONETEP is not compiled for multi-threading). To do this, the path to 'mkl_service.mod' should be given according to the CPU architecture and the length of MKL integer (see below).

The libs needed to be linked can be specified in keyword `LIBS`. It's tricky to set proper flags for MKL. Fortunately [Intel link line advisor webpage](https://www.intel.com/content/www/us/en/developer/tools/oneapi/onemkl-link-line-advisor.html#gs.487b5w) or attached app can offer suggested values according to the environment.

Versions of compiler and Linux distributions have been stated above. The compilation file for NHPC101 and Imperial CX1:

```
F90 = mpiifort
FFLAGS = -DMPI -I"${MKLROOT}/include" -DMKL_FFTW3 -I"${MKLROOT}/include/intel64/lp64/mkl_service.mod" -qmkl=parallel -DSCALAPACK -diag-disable 8290,8291,7712,5462 
LIBS = -L${MKLROOT}/lib/intel64 -lmkl_scalapack_lp64 -lmkl_cdft_core -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lmkl_blacs_intelmpi_lp64 -lpthread -lm -ldl

OPTFLAGS = -O3 -xhost
DEBUGFLAGS = -traceback -check all -traceback -warn all -DTRACE -DDEBUG_ARRAYS
COMPILER = INTEL-ifort-on-LINUX
```

Note that NHPC101 has limited stack size(8192). Use the following command every time before launching a calculation:

``` console
$ ulimit -s unlimited
```

## Multi-threading parallelization

The multi-threading version of compilation file for NHPC101 and Imperial CX1:

```
F90 = mpiifort
FFLAGS = -DMPI -I"${MKLROOT}/include" -DMKL_FFTW3 -I"${MKLROOT}/include/intel64/lp64/mkl_service.mod" -DParallelFFT -qmkl=parallel -DSCALAPACK -diag-disable 8290,8291,7712,5462 -qopenmp -DDEFAULT_THREADS=2
LIBS = -L${MKLROOT}/lib/intel64 -lmkl_scalapack_lp64 -lmkl_cdft_core -lmkl_blacs_intelmpi_lp64 -liomp5 -lpthread -lm -ldl

OPTFLAGS = -O3 -xhost
DEBUGFLAGS = -traceback -check all -traceback -warn all -DTRACE -DDEBUG_ARRAYS
COMPILER = INTEL-ifort-on-LINUX
```

`-DDEFAULT_THREADS=2` set the default `threads_max` and `threads_num_fftboxes` threading of each process to be 2. Using the following command before any calculation, the number of threading will cover the default value and increases to 4.

``` console
$ export OMP_NUM_THREADS=4
```

Multi-threading needs a separate setting of stack size. Use the following command before any calculation:

``` console
$ export OMP_STACKSIZE=64M
```

**NOTE**  
1. Compilation on CX1 does not support the option `-xhost`, which forces the use of the highest instruction set available, due to unknown reasons. Compilation is killed by signal when interpreting the object file, 'function_basis_mod.o', of the script 'function_basis_mod.F90'.    
2. The current compilation on CX1 is not compatible with test scripts on throughput nodes. In the best case, the job got stuck after printing the input information. Test scripts works well on login nodes.