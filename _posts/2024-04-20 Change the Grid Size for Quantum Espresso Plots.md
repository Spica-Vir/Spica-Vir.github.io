---
title: Change the Grid Size for Plots of Quantum Espresso
date: 2024-04-20 21:01:17 +0100
categories: [Research, DFT]
tags: [Quantum Espresso]
---

Earlier this week I wasted (or more neutrally, spent) a whole afternoon to read the source code of [`pp.x` of Quantum Espresso (QE) 7.1](https://www.quantum-espresso.org/Doc/INPUT_PP.html), which is a post-processing executable to analyze QE wavefunction files. Later I realized it was not a bug but I set wrong parameters. Another interesting finding is that I found a way to change the grid size of 3D plots in QE, which, for some reason, is lengthy and might not work well with released code. Though I put full credits to its developers I did not find anything suspicious for my tests. 

Disclaimer: **USE IT CAREFULLY ESPECIALLY WHEN YOU HAVE A NON-ORTHOGONAL CELL!!!** I am not responsible for that if anything goes wrong.

Tested on 17/04/2024.

# Problem
Usually the fastest and the most recommended way to get a 3D plot is plotting on all FFT grid points as charge densities and wavefunction are calculated on them and no extra interpolation is needed. The user might want to resample the whole region where data is defined by changing `nx` `ny` and `nz` (see [input description](https://www.quantum-espresso.org/Doc/INPUT_PP.html)) to either reduce or increase grid size, but it will not work in many cases even if those keywords are added.

# How to fix it
The issue is how the boolean `fast3d` is defined in the 'PP/src/chdens_module.f90' script of the released QE7.1. A quick way to fix it is adding a new keyword for that parameter so the user can force the fast 3D grid data generation (which is based on FFT grid) to be turned off. 

In line 103-106 of 'chdens_module.f90', add the new keyword option, `fast3d`:

``` fortran
  NAMELIST /plot/  &
       nfile, filepp, weight, iflag, e1, e2, e3, nx, ny, nz, x0, &
       radius, output_format, fileout, interpolation, fast3d, &
       isostm_flag, isovalue, heightmin, heightmax, direction
```

Also add the default value for that variable after line 130:

``` fortran
  fast3d        = .true.
```

Modify line 435-438, which was line 434-436 of unmodified file, into:

``` fortran
    fast3d = fast3d .and. &
             ( e1(2) == 0.d0  .and.  e1(3) == 0.d0) .and. &
             ( e2(1) == 0.d0  .and.  e2(3) == 0.d0) .and. &
             ( e3(1) == 0.d0  .and.  e3(2) == 0.d0)
```

All done. Then recompile codes in 'PP' folder and use the new `pp.x` executable.

# Input file and interpolation method
A new keyword `fast3d` is now added and recognizable by `pp.x`. To resample the 3D grid data of the same region, you need to set `fast3d = .false.` in the `&PLOT` block and set the new grid size by `nx`, `ny` and `nz`.

I tried `interpolation = 'fourier'` but it is as slow as the manual suggested. In fact my test system was so large that the calculation got stuck for 1 hour and hit the walltime, so I am not 100% sure this method works with Fourier interpolation. But `interpolation = 'bspline'` works quite smoothly without noticeable difference in the plotted isosurfaces. To me, the B-spline interpolation code also looks fine for such applications ('chdens_bspline.f90', same directory). The only thing that looks not correct to me is how the code shows its progress. '100% done` will probably never show up if the new grid is smaller than the 'dense' FFT grid as there are less grid points, in which case interpolation is actually done.

