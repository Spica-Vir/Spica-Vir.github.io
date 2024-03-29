---
title: CRYSTAL known issues
date: 2022-01-23 11:43:38 +0000
categories: [Research, DFT]
tags: [CRYSTAL, regular inspection]
---
In this post are listed some known issues about [CRYSTAL](https://www.crystal.unito.it/), including **CRYSTAL17 v1.0.2** and **CRYSTAL23 v1.0.1**. Only problems with its source code are included, for other issues related to usages, please refer to other posts. Note, the majority of the issues are 'imperfections' rather than 'bugs'. Special thanks to N.M.H, a main developer, for inspecting the source codes. 

Tests of the parallel edition are performed on [Imperial cluster](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/) and [ARCHER2](https://www.archer2.ac.uk/) UK national supercomputer. Tests of the serial Linux edition are performed on Debain WSL2, Windows 21H2. Tests are performed from Feb. 2021 till the last updated date. The contents of this webpage are checked regularly. 

# Basis set
`EIGS` *parallel only* - The bug in printout of overlap matrix eigenvalues
: The diagonalization of the overlap matrix is distributed to individual cores according to the number of irreducible k points during parallel computing. The core in charge of printing will end the output file early when **the number of cores used is larger than the number of k points** due to the signal from spare cores. No eigenvalue will be printed out in this case. Only the exact `TELAPSE` and `TCPU` are printed at the end of the output file. 

: **SOLUTION** The number of cores should be smaller than the number of irreducible k points (see the pre-SCF section of the `.out` file) when parallel CRYSTAL is used. 

`BASISSET` - A noticeable issue about its incompatibility with the `END` keyword. 
: When specifying the pre-defined basis set names using `BASISSET`, the `END` keywords of geometry block and basis set block should be removed in case of error in reading input deck. 

`BASISSET` + `def2-SVP` - *CRYSTAL23* A noticeable issue about the obsolete warning message
: The following warning message is obsolete and not true.

``` text
 !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! WARNING !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! 
 This is a molecular basis set! It might contain basis functions with an angular
 momentum (AM) higher than 3 (e.g. g-funcitons). Since CRYSTAL only supports
 functions with an AM of up to 3 (f-functions) they are removed from the basis.
 Keep that in mind when comparing results to molecular codes!
 !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```
: **SOLUTION** Ignore it. 

# Exchange-Correlation functional & related
`HYBRID` `SR-OMEGA` `MR-OMEGA` `LR-OMEGA` *both editions* - A noticeable issue about their incompatibility. 
: Fock hybrid percentage and short/medium/long rangeness cannot be modified simultaneously. `SR-OMEGA`, `MR-OMEGA`, and `LR-OMEGA` are only applicable to pre-defined range-separated functionals. 

: **SOLUTION** The current situation is acceptable unless for developing new hybrid functionals, which is a delicate thing. The code also supports user-defined range separation of PBE based hybrid functionals - the keyword `LSRSH-PBE` is compatible with `HYBRID`.

`SR-HYB_WB97X` - A bug with the keyword
: Used for tuning the short-range HF mixing percentage. Does not work at all because the code cannot recognize 'wB97X'. 

: **SOLUTION** Unknown. 

`DFTD3` `gCP` - A noticeable issue that this sub-block is closed by `END` keyword only. 
: Code does not recognize `END` keywords with suffixes for differentiating proposes, such as `ENDD3`. 

`FUNC` of `DFTD3` - A bug that some functionals claimed to support actually cannot perform auto-parameterization. 
: Test performed on M06-2X functional. For the list of available functionals please refer to [Obtain parameter lists for Grimme's DFT-D3 and gCP methods](https://spica-vir.github.io/posts/Parameter-list-for-Grimme's-DFT-D3-and-gCP-method/). 

: **SOLUTION** Turn to the current coefficient pages ([BJ damping](https://www.chemie.uni-bonn.de/pctc/mulliken-center/software/dft-d3/functionalsbj) and [zero damping](https://www.chemie.uni-bonn.de/pctc/mulliken-center/software/dft-d3/functionals)) of DFT-D3 for parameters and enter them manually. 

`FUNC` of `DFTD3` *Parallel edition* - A noticeable issue about incompatibility of newly published functionals
: `DFTD3` only recognizes the supported functionals and that goes beyond its own block. `FUNC` keyword will not cover the setups if a functional unsupported is defined in `DFT` block, which means users cannot cheat the code with a supported functional name at `FUNC` keyword and manually modify all parameters for the functional actually used. 

: **SOLUTION** Unknown.

# Geometry optimization
`ITATOCEL` `RESTART` - An incompatible bug in certain conditions. 
: Cell parameter optimization is launched as the initial step of `ITATOCEL`. If the optimization is interrupted at the atomic coordinate optimization step, the restarted optimization will still begin from the cell parameters, leading to the error message `OPTIMIZATION TYPE CHANGED`. 

: **SOLUTION** Manually set the option to `ATOMONLY` and restart. Then use `ITATOCEL` to check the convergence of cell parameters again. 

# Frequency & DFPT
`PHONBANDS.DAT` - *CRYSTAL17, 23 not tested* The bug in printout of phonon bands
: The discontinuous points of phonon dispersion curves at the high-symmetric points originate from a bug in source code. The 2 ends of every segment are duplicated during calculation, which the printout option fails to consider. 

: **SOLUTION** Use the fort.25 file instead. 

`MODES` - The change of default settings
: The `MODES` command is the default option for most of cases. Howver, it is deactivated by the `BANDS` command, with the information `INFORMATION **** INPFREQ **** BANDS: MODES PRINTING DISABLED. USE KEYWORD MODES TO ENABLE.`.

: **Solution** To examine the eigenvectors of mass-weighted Hessian matrices during phonon band calculations, insert `MODES` in the `FREQCALC` block. Or an alternative is suggested to make the output clearer: Run frequency calculations at Γ first and restart the calculations for phonon bands. 


`fort.9` tested on - A noticeable issue about empty binary wavefunction file for restarted jobs
: Unlike optimization module, the frequency module does not save binary wavefunction files (fort.9) of subsequent SCF calculations, so the fort.9 file for a restarted calculation is empty. 

: **SOLUTION** Use the fort.9 file of the initial run. 

`BIPOSIZE` - A memory allocation bug about exceeding disk quota 
: The keyword `BIPOSIZE` defines the size of bi-electron integral buffer. By default it is 32Mb. That limit might be exceeded when using large basis sets / screened hybrid functionals. Typically that issue can only lead to a warning message during SCF calculations and slightly sluggish computation, because the system has to re-allocate disk quota and restart calculations after the bi-electron integral exceeds the pre-defined buffer. 

: However, such re-allocation is neither saved in SCF output files nor passed to the frequency calculation module. When involving a new SCF calculation 'within' the module (the known examples are `RESTART` and `QHA`), 'Disk quota exceeded' error will be reported and the program will exit without re-allocating the disk quota. 

: **NOTE** According to tests, missing `BIPOSIZE` might kill other running jobs as well. 

: **SOLUTION** For `RESTART`, do an SCF trial run for large systems and set `BIPOSIZE` no smaller than the output value. For `QHA`, no solution is known since it inevitably launches multiple harmonic calculations.

`MULTITASK` - A file I/O bug about restarting multitask calculations
: This keyword allows multiple SCF calculations running simultaneously when generating the numerical force constant matrices. The FREQINFO.DAT.tsk\[n\] files are generated during the calculation and the FREQINFO.DAT file is generated at the end. Restarting a `MULTITASK` run with either FREQINFO.DAT.tsk\[n\] or FREQINFO.DAT file is impossible, no matter whether the previous run is either finished or killed.

: **SOLUTION** Unknown.

# Properties

`CPHF` - A noticeable issue about supported exchange-correlation functionals. 
: Only a limited number of functionals are supported in CPHF/KS method. Listed in the manual.  

`PBAN` - A noticeable issue about projected charge density of conduction bands. 
: Charge density projection are obtained directly from KS eigenvalues and will be exactly 0 if projected onto unoccupied bands, which is different from DOS curves, in which case the density above the Fermi level is non-zero. 

: **SOLUTION** To visualize the spatial shape of CBM, compute crystalline orbitals with the keyword `ORBITALS` instead. 

# Developers

`BASISSET` + 'def2' basis set family - A noticeable issue that non-UTF-8 encoded character is printed out when specifying `BASISSET` with 'def2' basis sets.  
: The non-UTF-8 encoded character lies in the 'ä' of the following line:

```
Fully Optimized Contracted Gaussian Basis Sets for Atoms Li to Kr. A. Schäfer, H. Horn and R. Ahlrichs; J. Chem. Phys. 97, 2571 (1992). 
```  

This issue leads to probable errors when decoding files. For example, when using reading files with python:

``` python
>>> file = read(filename, 'r')
```

The command above leads to error message: 'Non UTF-8 encoded character.' Either delete 'ä' or add the option to skip the character can solve this problem:

``` python
>>> file = read(filename, 'r', errors='ignore')
```
