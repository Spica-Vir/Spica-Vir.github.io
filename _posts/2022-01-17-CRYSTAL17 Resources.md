---
title: CRYSTAL17 Resources
date: 2022-01-17 17:23:43 +0000
categories: [Research, DFT]
tags: [software, CRYSTAL]
---

Some useful resources for [CRYSTAL17](https://www.crystal.unito.it/index.php) learning and usage are listed in this page. All resources listed here are available online free of charge. Those currently not well-maintained ones are marked. Special thanks to my supervisors, N.M.H & G.M, and my colleague F.B. for kindly providing their suggestions. Last modified on Jan. 17-21. 

Current public release: **CRYSTAL17 v1.0.2**

# Manual & Tutorials
[**User manual**](https://www.crystal.unito.it/Manuals/crystal17.pdf) - The 'bible' of CRYSTAL17.  

[**CRYSTAL tutorial project**](http://tutorials.crystalsolutions.eu/index.html) - A collection of specially designed tutorials and workshops  

This website, of course :-)  

# Basis set
[**The officially listed BSs**](https://www.crystal.unito.it/compounds.php) - Published tests on periodic systems are available.

[**Basis Set Exchange (BSE)**](https://www.basissetexchange.org/) - Tests on linear dependency needed. Available BSs are usually optimized for molecular systems.

A collection of system-specific, Gaussian BSs optimized by [**Dr Mike Towler**](https://vallico.net/mike_towler/crystal.html). **Not maintained anymore**.

# Geometry & Visualization
A main drawback of CRYSTAL package is the lack of matching geometry edit & visualization kit, making it comparatively not as user friendly as more popular codes like VASP/CASTEP. It should be noted that even though links here provide some temporary solutions, the images obtained are typically not satisfying from a point of publication, so coding seems to be the only choice for now. Meanwhile, for some keywords, there are print options for formats widely supported by mainstream visualization software - a typical example is the .CUBE format by the ECH3 keyword - which will not be discussed any more. Anyway, it should not be a real problem for any researcher with the basic programming capability. Maybe I will post my scripts to github someday when I am free and happy to do so. 

[**CRYSPLOT**](http://crysplot.crystalsolutions.eu/) - An all-in-one visualization solution

Known issues  
: - fort.25 (f25) file for DOSS/COOP/COHP: The Fermi level is not aligned to zero as routinely adopted. The calculated Fermi energy should be added to the values of the energy axis.  
: - PHONBANDS.DAT file for phonon band calculation: A bug with the print option of CRYSTAL17. Data on reciprocal points next to the high-symmetric points might go missing - use the fort.25 file instead.  
: - [MOLDRAW](https://www.moldraw.unito.it/_sgg/f10000.htm) (visualizing the optimization steps and lattice vibrations) and [TOPOND](https://www.crystal.unito.it/topond/topond.php) (topological analysis of electron density) have been integrated to [CRYSPLOT](http://crysplot.crystalsolutions.eu/).  

[**Jmol**](http://jmol.sourceforge.net/) - Non-periodic system visualizer. The lattice boundaries can be visualized, but the atoms cannot be replicated along periodic directions. Used to visualize the molecular or crystalline orbitals (.molden file).

[**DL Visualize (DLV)**](http://www.cse.clrc.ac.uk/cmg/DLV/) **Not maintained anymore**. Developed in Daresbury laboratory. Its website has been unavailable since late 2021, so regarded as not maintained even though there are rumors that there is a plan to re-write codes of DLV. 

[**FINDSYM**](https://stokes.byu.edu/iso/findsym.php) - Identify the space group from a CIF structure file and provide fractional coordinates of all the nonequivalent atomic sites. 

[**Atomic simulation environment (ASE)**](https://www.databases.fysik.dtu.dk/ase/ase/io/formatoptions.html?highlight=crystal%20write#ase.io.crystal.write_crystal) - A module to read and write the fort.34 (gui) formatted geometry file. 

Known issues
: - The `ase.io.crystal.write_crystal` method has no symmetry analysis. Its output only has the P1 symmetry.  
: - ASE also has an [interface](https://www.databases.fysik.dtu.dk/ase/ase/calculators/crystal.html?highlight=crystal#module-ase.calculators.crystal) to CRYSTAL14 to enable DFT/HF calculations within ASE.  

# Appendix: the output naming scheme
A table is provided here to list the current naming schemes of the simulation outputs in my research group, with modifications. The original naming scheme can be found on the Imperial cluster. For CRYSTAL17 on ARCHER2 and my personal server, the naming scheme strictly follows the table below. 

Note: The input file and job submission file are named as `jobname.xxx`. 

|  NAME               | FORMAT         | DESCRIPTION                           |
|:--------------------|:---------------|:--------------------------------------|
| *Crystal calculations (.d12)* |      |                                       |
|  jobname.gui        | fort.34        | geometry input                        |
|  jobname.xyz        | fort.33        | geometry, atom coordinates only       |
|  jobname.GAUSSIAN   | GAUSSIAN.DAT   | input for Gaussian98/03               |
|  jobname.FINDSYM    | FINDSYM.DAT    | input format for findsym              |
|  jobname.ERROR      | fort.87        | error report                          |
|  jobname.f9         | fort.9         | binary wavefunction                   |
|  jobname.f98        | fort.98        | formatted wavefunction                |
|  jobname.PPAN       | PPAN.DAT       | mulliken population                   |
|  jobname.f20        | fort.20        | SCF guess (last step density matrix)  |
|  jobname.HESSOPT    | HESSOPT.DAT    | Hessian matrix per optimisation step  |
|  jobname.OPTINFO    | OPTINFO.DAT    | optimisation restart data             |
|  jobname.optstory/  | opt*           | optimised geometry per step           |
|  jobname.SCFLOG     | SCFOUT.LOG     | SCF output per optimisation step      |
|  jobname.FREQINFO   | FREQINFO.DAT   | frequency restart data                |
|  jobname.f13        | fort.13        | binary reducible density matrix       |
|  jobname.f28        | fort.28        | binary IR intensity restart data      |
|  jobname.f80        | fort.80        | binary localised Wannier funcions     |
|  jobname.f25        | fort.25        | Phonon bands Crgra2006 format         |
|  jobname.PHONBANDS  | PHONBANDS.DAT  | Phonon bands xmgrace format           |
|  jobname.scanmode/  | SCAN*_DISP*    | Displaced .gui along scanned mode     |
| *Property calculations (.d3)* |      |                                       |
|  jobname.f25        | fort.25        | all Crgra2006 format data             |
|  jobname.BAND       | BAND.DAT       | band xmgrace format                   |
|  jobname.DOSS       | BAND.DOSS      | dos xmgrace format                    |
|  jobname.prop3d     | fort.31        | all 3D grid data                      |
|  jobname_CHG.CUBE   | DENS_CUBE.DAT  | 3D charge density Gaussian CUBE format|
|  jobname_SPIN.CUBE  | SPIN_CUBE.DAT  | 3D spin density CUBE format           |
|  jobname_POT.CUBE   | POT_CUBE.DAT   | 3D electrostatic potential CUBE format|
|  jobname.POTC       | POTC.DAT       | 1D electrostatic potential            |
|  jobname.DIEL       | DIEL.DAT       | dielectric constant                   |
