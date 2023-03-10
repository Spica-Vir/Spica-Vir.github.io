---
title: CRYSTAL Resources
date: 2022-01-17 17:23:43 +0000
categories: [Research, DFT]
tags: [CRYSTAL, regular inspection]
---

Some useful resources for [CRYSTAL](https://www.crystal.unito.it/index.html) learning and usage are listed in this page. All resources listed here are available online free of charge. Those currently not well-maintained ones are marked. Special thanks to my supervisors, N.M.H & G.M, and my colleague F.B. for kindly providing their suggestions. Contents of this page are regularly inspected. 

Current public release: **CRYSTAL23 v1.0.1**

# Manual & Tutorials
[**User manual**](https://www.crystal.unito.it/include/manuals/crystal23.pdf) - The 'bible' of all versions of CRYSTAL.  

[**CRYSTAL tutorial project**](https://tutorials.crystalsolutions.eu/) - A collection of specially designed tutorials and workshops  

This website, of course :-)  

# Basis set
[**The officially listed BSs**](https://www.crystal.unito.it/basis_sets.html) - Published tests on periodic systems are available.

[**Basis Set Exchange (BSE)**](https://www.basissetexchange.org/) - Tests on linear dependency needed. Available BSs are usually optimized for molecular systems.

A collection of system-specific, Gaussian BSs optimized by [**Dr Mike Towler**](https://vallico.net/mike_towler/crystal.html). **Not maintained anymore**.

# Geometry, Visualization and Data processing
A main drawback of CRYSTAL package is the lack of matching geometry edit & visualization kit, making it comparatively not as user friendly as more popular codes like VASP/CASTEP. It should be noted that even though links here provide some temporary solutions, the images obtained are typically not satisfying from a point of publication, so coding seems to be the only choice for now. Meanwhile, for some keywords, there are print options for formats widely supported by mainstream visualization software - a typical example is the .CUBE format by the ECH3 keyword - which will not be discussed any more. Anyway, it should not be a real problem for any researcher with the basic programming capability. Maybe I will post my scripts to github someday when I am free and happy to do so. 

[**CRYSPLOT**](http://crysplot.crystalsolutions.eu/) - An all-in-one visualization solution

Known issues  
: - fort.25 (f25) file for DOSS/COOP/COHP: The Fermi level is not aligned to zero as routinely adopted. The calculated Fermi energy should be added to the values of the energy axis.  
: - PHONBANDS.DAT file for phonon band calculation: A bug with the print option of CRYSTAL17 source code. Data on reciprocal points next to the high-symmetric points might go missing - use the fort.25 file instead.  
: - [MOLDRAW](https://www.moldraw.unito.it/_sgg/f10000.htm) (visualizing the optimization steps and lattice vibrations) and [TOPOND](https://www.crystal.unito.it/topond/topond.php) (topological analysis of electron density) have been integrated to [CRYSPLOT](http://crysplot.crystalsolutions.eu/).  

[**Jmol**](http://jmol.sourceforge.net/) - Non-periodic system visualizer. The lattice boundaries can be visualized, but the atoms cannot be replicated along periodic directions. Used to visualize the molecular or crystalline orbitals (.molden file).

[**J-ICE**](http://j-ice.sourceforge.net/) - *Not tested* A web-based, crystallographic version of [Jmol](http://jmol.sourceforge.net/). 

[**DL Visualize (DLV) 3**](https://gitlab.com/bmgcsc/dlv3-release) Developed in Daresbury laboratory, mainly by B. Searle. There was a release webpage which requires registration, but due to unknown reasons, now instead of the webpage, its repository is accessible, so currently it is open-source and free of charge.

[**FINDSYM**](https://stokes.byu.edu/iso/findsym.php) - Identify the space group from a CIF structure file and provide fractional coordinates of all the nonequivalent atomic sites. 

[**Atomic simulation environment (ASE)**](https://www.databases.fysik.dtu.dk/ase/ase/io/formatoptions.html?highlight=crystal%20write#ase.io.crystal.write_crystal) - A module to read and write the fort.34 (gui) formatted geometry file. 

Known issues
: - The `ase.io.crystal.write_crystal` method has no symmetry analysis. Its output only has the P1 symmetry.  
: - ASE also has an [interface](https://www.databases.fysik.dtu.dk/ase/ase/calculators/crystal.html?highlight=crystal#module-ase.calculators.crystal) to CRYSTAL14 to enable DFT/HF calculations within ASE.  

[**CRYSTALpytools with workflows**](https://github.com/crystal-code-tools/CRYSTALpytools) **In development** - A pre- and post- processing tool co-developed by [Computational Materials Science Group](https://cmsg-icl.github.io/web/index.html) at Imperial College and [Theoretical Chemistry Group](https://www.chimica.unito.it/do/gruppi.pl/Show?_id=7myq) at University of Torino. It adopts the heavily object-oriented fashion of [Pymatgen](https://pymatgen.org/) and can easily cooperate with it. The latest stable release is accessible via `pip`. Installation instructions can be found on [this page](https://github.com/crystal-code-tools/CRYSTALpytools).

# Job submission scripts
There are several job submission scripts available online. Considering the pros and cons of available scripts, I developed a new version for [Imperial College HPC (CX1)](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/) based on Dr G. M.'s good example, which can be found on the [Imperial-HPC-Jb-Submission page](https://github.com/cmsg-icl/crystal_shape_control/tree/main/Imperial-HPC-Job-Submission) of the group github repository. Also check [Job_Submitter_specific page](https://github.com/cmsg-icl/crystal_shape_control/tree/main/Job_Submitter_specific/CRYSTAL) for other environments.

Note the setups of jobsubmitters are highly dependent on the environment of clusters. Currently 3 versions are developed, which covers the most common cases:

1. For PBS batch system, using the Imperial College HPC (CX1) as the target environment.  
2. For Slurm batch system, using the UK national super-computing service [ARCHER2](https://www.archer2.ac.uk/) as the target environment.  
3. For local servers without batch system. Both serial and parallel versions of CRYSTAL are supported.

# Appendix: the output naming scheme
A table is provided here to list the current naming schemes of the simulation outputs in my research group, with modifications. Even though this page is regularly inspection, oversight is inevitable. The complete and up-to-date naming scheme is available in the `settings` files of the job submission script folders. 

The input file and job submission file are named as `jobname.xxx`. 

|  NAME               | FORMAT            | DESCRIPTION                           |
|:--------------------|:------------------|:--------------------------------------|
| *Computation setups*                                                            |
|  jobname.d12        | INPUT             | crystal input file                    |
|  jobname.d3         | INPUT             | properties input file                 |
|  jobname.GAUSSIAN   | GAUSSIAN.DAT      | input in format of Gaussian98/03      |
| *Geometry information*                                                          |
|  jobname.gui        | fort.34           | geometry, periodic                    |
|  jobname.xyz        | fort.33           | geometry, atom coordinates only       |
|  jobname.cif        | GEOMETRY.CIF      | geometry, cif format                  |
|  jobname.STRUC      | STRUC.INCOOR      | geometry, STRUC.INCOOR format         |
|  jobname.FINDSYM    | FINDSYM.DAT       | input format for findsym              |
| *General output*                                                                |
|  jobname.ERROR      | fort.87           | error report                          |
|  jobname.out        | -                 | screen output of program              |
| *SCF*                                                                           |
|  jobname.f9         | fort.20, fort.9*  | binary wavefunction                   |
|  jobname.f98        | fort.98           | formatted wavefunction                |
|  jobname.SCFLOG     | SCFOUT.LOG        | SCF output of subsequent steps        |
|  jobname.PPAN       | PPAN.DAT          | mulliken population                   |
|  jobname.GRED       | GRED.DAT          | real space wavefunction for cryapi_inp |
|  jobname.KRED       | KRED.DAT          | k space wavefunction for cryapi_inp   |
| *Optimization*                                                                  |
|  jobname.HESSOPT    | HESSOPT.DAT       | Hessian matrix per optimisation step  |
|  jobname.OPTINFO    | OPTINFO.DAT       | optimisation restart data             |
|  jobname.optstory/  | opt*              | optimised geometry per step           |
| *Frequency & spectra*                                                           |
|  jobname.FREQINFO   | FREQINFO.DAT      | frequency restart data                |
|  jobname.f13        | fort.13           | binary reducible density matrix       |
|  jobname.f28        | fort.28           | binary IR intensity restart data      |
|  jobname.f81        | fort.81, fort.80* | binary localized Wannier function     |
|  jobname.f25        | fort.25           | Phonon bands Crgra2006 format         |
|  jobname.PHONBANDS  | PHONBANDS.DAT     | Phonon bands xmgrace format           |
|  jobname.scanmode/  | SCAN*_DISP*       | Displaced .gui along scanned mode     |
|  jobname.IRDIEL     | IRDIEL.DAT        | IR dielectric function                |
|  jobname.IRREFR     | IRREFR.DAT        | IR refractive index                   |
|  jobname.IRSPEC     | IRSPEC.DAT        | IR absorbance and reflectance         |
|  jobname.BORN       | BORN.DAT          | Born tensor                           |
|  jobname.RAMSPEC    | RAMSPEC.DAT       | Raman spectra                         |
|  jobname.TENS_RAMAN | TENS_RAMAN.DAT    | Raman tensor                          |
|  jobname.EOSINFO    | EOSINFO.DAT       | QHA and equation of states data       |
| *CPHF/KS & dielectric constants*                                                |
|  jobname.f32        | fort.31, fort.32* | CPHF/KS restart data                  |
|  jobname.DIEL       | DIEL.DAT          | dielectric constant                   |
| *Electronic bands & density of states*                                          |
|  jobname.f25        | fort.25           | all Crgra2006 format data             |
|  jobname.BAND       | BAND.DAT          | band xmgrace format                   |
|  jobname.DOSS       | BAND.DOSS         | dos xmgrace format                    |
| *Charge & 3D grid data*                                                         |
|  jobname.f31        | fort.31        | all 3D grid data                         |
|  jobname_CHG.CUBE   | DENS_CUBE.DAT  | 3D charge density Gaussian CUBE format   |
|  jobname_SPIN.CUBE  | SPIN_CUBE.DAT  | 3D spin density CUBE format              |
|  jobname.RHOLINE    | RHOLINE.DAT    | 1D charge density and gradient           |
|  jobname.POINTCHG   | POINTCHG.INP   | Set dummy atoms with given charge        |
|  jobname_POT.CUBE   | POT_CUBE.DAT   | 3D electrostatic potential CUBE format   |
|  jobname.POTC       | POTC.DAT       | 1D electrostatic potential               |
| *Molecular & crystalline orbitals*                                              |
|  jobname.molden     | defname.molden | molecular/crystalline orbitals for Jmol  |

**Note** Dual file names in the middle column marked with '\*' stand for input file name (left) and output file name (right) respectively. Files are duplicated during calculation in case of losing important data.
