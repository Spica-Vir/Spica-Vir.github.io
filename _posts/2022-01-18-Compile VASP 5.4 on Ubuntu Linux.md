---
title: Compile VASP 5.4 on Ubuntu/Debain Linux
date: 2022-01-18 17:07:32 +0000
categories: [Technique, Parallel Computing]
tags: [software, VASP]
---

This post is to briefly introduce available methods to compile VASP 5.4 on Ubuntu or Debain OSs. Tested on Debian WSL2, Windows 10 21H2, Jan 9-21. VASP edition 5.4.1.05Feb16. 

Currently there are two mainstream compiling and parallelizing solutions for VASP: by Intel parallel studio + Ifortran, or by openmpi + gfortran. The former one is an almost 'all-in-one' solution and easier to compile, but a valid license number is needed, which can be requested free of charge via educational/academic email addresses. The latter one is based on GNU license and free of charge, but more difficult - libraries of gfortran, openmpi, and ScaLAPACK are needed and package dependency should be carefully checked. According to many online tests, if parameters in the 'makefile.include' file (see below) are set properly, there is only a negligible difference in performances of VASP running on Intel package or GNU packages. 

# By Intel
## A recent issue with Intel parallel studio

## Prerequisites



# By GNU