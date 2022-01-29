---
title: CRYSTAL17 known issues
date: 2022-01-23 11:43:38 +0000
categories: [Research, DFT]
tags: [CRYSTAL, regular inspection]
---
In this post are listed some known issues about the current official edition of CRYSTAL, [**CRYSTAL17 v1.0.2**](https://www.crystal.unito.it/index.php). Only problems with its source code are included, for other issues related to usages, please refer to other posts. Note, the majority of the issues are 'imperfections' rather than 'bugs'. Special thanks to N.M.H, a main developer, for inspecting the source codes. 

Tests of the parallel edition are performed on [Imperial cluster](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/) and [ARCHER2](https://www.archer2.ac.uk/) UK national supercomputer. Tests of the serial Linux edition are performed on Debain WSL2, Windows 21H2. Tests are performed from Feb. 2021 till the last updated date. The contents of this webpage are checked regularly. 

# Basis set
`EIGS` *parallel edition* - The bug in printout of overlap matrix eigenvalues
: The diagonalization of the overlap matrix is distributed to individual cores according to the number of irreducible k points during parallel computing. The core in charge of printing will end the output file early when **the number of cores used is larger than the number of k points** due to the signal from spare cores. No eigenvalue will be printed out in this case. Only the exact `TELAPSE` and `TCPU` are printed at the end of the output file. 

: **SOLUTION** The number of cores should be smaller than the number of irreducible k points (see the pre-SCF section of the `.out` file) when parallel CRYSTAL is used. 

`LDREMO` *both editions* - Not available.
: This command is an old keyword to remove linear dependency of basis sets by removing linear combinations of Gaussian orbitals with small eigenvalues from the overlap matrix. Not available in CRYSTAL17 manual as well. 

: **SOLUTION** Unknown.

`BASISSET` *both editions* - A noticeable issue about its incompatibility with the `END` keyword. 
: When specifying the pre-defined basis set names using `BASISSET`, the `END` keywords of geometry block and basis set block should be removed in case of error in reading input deck. 


# Exchange-Correlation functional
`HYBRID` `SR-OMEGA` `MR-OMEGA` `LR-OMEGA` *both editions* - A noticeable issue about their incompatibility. 
: Fock hybrid percentage and short/medium/long rangeness cannot be modified simultaneously. `SR-OMEGA`, `MR-OMEGA`, and `LR-OMEGA` are only applicable to pre-defined range-separated functionals. 

: **SOLUTION** The current situation is acceptable unless for developing new hybrid functionals, which is a delicate thing. The code also supports user-defined range separation of PBE based hybrid functionals - the keyword `LSRSH-PBE` is compatible with `HYBRID`. 

# Geometry optimization
`ITATOCEL` `RESTART` *both editions* - An incompatible bug in certain conditions. 
: Cell parameter optimization is launched as the initial step of `ITATOCEL`. If the optimization is interrupted at the atomic coordinate optimization step, the restarted optimization will still begin from the cell parameters, leading to the error message `OPTIMIZATION TYPE CHANGED`. 

: **SOLUTION** Manually set the option to `ATOMONLY` and restart. Then use `ITATOCEL` to check the convergence of cell parameters again. 

# Frequency & DFPT
`PHONBANDS.DAT` *both editions* - The bug in printout of phonon bands
: The discontinuous points of phonon dispersion curves at the high-symmetric points originate from a bug in source code. The 2 ends of every segment are duplicated during calculation, which the printout option fails to consider. 

: **SOLUTION** Use the fort.25 file instead. 

`MODES` *both editions* - The change of default settings
: The `MODES` command is the default option for most of cases. Howver, it is deactivated by the `BANDS` command, with the information `INFORMATION **** INPFREQ **** BANDS: MODES PRINTING DISABLED. USE KEYWORD MODES TO ENABLE.`.

: **Solution** To examine the eigenvectors of mass-weighted Hessian matrices during phonon band calculations, insert `MODES` in the `FREQCALC` block. Or an alternative is suggested to make the output clearer: Run frequency calculations at Γ first and restart the calculations for phonon bands. 

# Properties
`CPHF` *both editions* - A noticeable issue about supported exchange-correlation functionals. 
: Only a limited number of functionals are supported in CPHF/KS method. Listed in the manual.  

`PBAN` *both editions* - A noticeable issue about projected charge density of conduction bands. 
: Charge density projection are obtained directly from KS eigenvalues and will be exactly 0 if projected onto unoccupied bands, which is different from DOS curves, in which case the density above the Fermi level is non-zero. 

: **SOLUTION** To visualize the spatial shape of CBM, compute crystalline orbitals with the keyword `ORBITALS` instead. 

# Developers
`SETINF` *both editions* - Not available.
: Set the value of INF array in source code. The list of specific definitions of INF array is not available for released editions. `SETINF` is available in the manual, but ineffective in practical calculations. 

: **SOLUTION** Unknown. 