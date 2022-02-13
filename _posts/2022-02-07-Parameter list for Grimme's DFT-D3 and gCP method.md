---
title: Obtain paramter lists for Grimme's DFT-D3 and gCP methods
date: 2022-02-07 15:14:57 +0000
categories: [Research, DFT]
tags: [Empirical correction, molecular crystals]
---

This is a short note discussing how to obtain the critical parameters for empirical DFT-D3 and geometric counterpoise (gCP) corrections developed in [S. Grimme's group](https://www.chemie.uni-bonn.de/pctc/mulliken-center/grimme/welcome). For DFT-D3, part of the parameters are available from its original publication, yet not complete. For gCP, no data is given in published papers. However, they can be easily obtained from the source code of the software posted in group webpage - [DFT-D3](https://www.chemie.uni-bonn.de/pctc/mulliken-center/software/dft-d3/dft-d3) and [gCP](https://www.chemie.uni-bonn.de/pctc/mulliken-center/software/gcp). They are actually toolkit written in Fortran90 to calculate dispersion energy and gCP energy correction terms of a given geometry and based on a given level of accuracy. 

The latest releases available on Feb. 07-22 are adopted: DFT-D3 v3.2 Rev0 and gCP v2.02. 

# DFT-D3


# gCP

Parameters of gCP mainly depend on basis sets. Open the script 'gcp.f90'. The following table lists the name of critical parameters and their corresponding variables in the script (line 459~462): 

| **CRYSTAL variables** | SIGMA | ALPHA | BETA | ETA  |
|:----------------------|:-----:|:-----:|:-- -:|:----:|
| **gCP variables**     | P(1)  | P(3)  | P(4) | P(2) |

Note that there are 2 other variables in the script, `emiss` and `nbas`, which are basis set and element specific. They are defined by the keyword `METHOD` and the geometry file in CRYSTAL, and therefore cannot be modified. Their values are stored respectively in `HFbsname` and `BASbsname` variables in the same script (line 1055~1302), e.g., `HFsv` and `BASsv`. 

The variable `RADIUS` in CRYSTAL `GCP` subblock is doubtful here and modification is not recommended. According to the definition in the manual (p.p. 139), it should correspond to the variable `thrR`, in the script it equals 60 Bohr and offers no help, because it is not a fitted parameter but a cutoff radius for summation. 

If it means the radius of atom pairs used for gCP calculation, it should be element specific. In line 582~593, cutoff radius from H to Pu are defined in variable `rad` but not used. `r0ab` defines the cut-off radii for all element pairs. Values (in Bohr) can be found in line 4017~4800, defined in a pairwise fashion - in total 4465 combinations, almost unreadable. 

The supported methods and basis sets are listed in the manual. According to my rough comparison, the list seems to be incomplete. Their corresponding variable names are listed in:

* **line 1311~1433** - HF  
* **line 1435~1574** - KS-DFT & B3LYP  
* **line 1578~1661** - 3c & double-hybrid XC functional  

For CRYSTAL, the supported methods are consistent with those listed in the script 'gCP.f90'. Even though the supported methods are not listed in the manual, they can be viewed by a test run. Supported values are printed out if the value of `method` in `GCP` sub-block is not recognized, which are listed below: 

**CRYSTAL17 available options & default parameters - gCP v2.02**

| KEYWORD                                                              | SIGMA  | ALPHA  | BETA   | ETA    |
|:---------------------------------------------------------------------|:------:|:------:|:------:|:------:|
| *Hartree-Fock*                                                                                           |
| HF/SV                                                                | 0.1724 | 0.8568 | 1.2342 | 1.2804 |
| HF/SVP, HF/DEF2SVP                                                   | 0.2054 | 0.8136 | 1.2572 | 1.3157 |
| HF/SV(P), HF/DEF2SV(P), HF/SV_P (*)                                  | 0.1373 | 0.8141 | 1.2760 | 1.4271 |
| HF/DZP                                                               | 0.1443 | 0.3711 | 1.6300 | 1.4547 |
| HF/631GD                                                             | 0.2048 | 0.9447 | 1.2100 | 1.5652 |
| HF/MINIS, HF/MINIX                                                   | 0.1290 | 1.1549 | 1.1763 | 1.1526 |
| HF/DEF2TZVP                                                          | 0.3127 | 1.0216 | 1.2833 | 1.9914 |
| HF/DEFTZVP, HF/TZVP                                                  | 0.2600 | 0.7998 | 1.4381 | 2.2448 |
| HF/CCDZ, HF/CCPVDZ                                                   | 0.4416 | 0.6902 | 1.3713 | 1.5185 |
| HF/ACCDZ, HF/AUGCCDZ                                                 | 0.0748 | 0.3811 | 1.0155 | 0.0663 |
| HF/2G                                                                | 0.2461 | 0.7335 | 1.4709 | 1.1616 |
| *KS-DFT*                                                                                                 |
| DFT/LANL, B3LYP/LANL                                                 | 0.3405 | 0.8589 | 1.2830 | 1.6127 |
| DFT/SV, B3LYP/SV                                                     | 0.4048 | 0.8652 | 1.2375 | 1.1626 |
| DFT/SV(P), B3LYP/SV(P), DFT/DEF2SV(P), B3LYP/DEF2SV(P) (*), DFT/SV_P | 0.2424 | 0.6076 | 1.4078 | 1.2371 |
| DFT/SVX, B3LYP/SVX                                                   | 0.1861 | 0.6171 | 1.4019 | 1.3200 |
| DFT/SVP, B3LYP/SVP, DFT/DEF2SVP, B3LYP/DEF2SVP                       | 0.2990 | 0.6438 | 1.3694 | 1.2605 |
| DFT/DZP                                                              | 0.2687 | 0.3513 | 1.6880 | 1.4634 |
| DFT/631GD, B3LYP/631GD                                               | 0.3405 | 0.8589 | 1.2830 | 1.6127 |
| DFT/MINIS, B3LYP/MINIS                                               | 0.2059 | 1.1961 | 1.1456 | 0.9722 |
| DFT/TZ, B3LYP/TZ, DFT/DEF2TZVP, B3LYP/DEF2TZVP                       | 0.2905 | 0.8120 | 1.4412 | 2.2495 |
| DFT/DEFTZVP, B3LYP/DEFTZVP, DFT/TZVP, B3LYP/TZVP                     | 0.2393 | 0.8185 | 1.4298 | 2.2247 |
| DFT/CCDZ, B3LYP/CCDZ, DFT/CCPVDZ, B3LYP/CCPVDZ                       | 0.5383 | 0.6230 | 1.4523 | 1.6482 |
| DFT/ACCDZ, B3LYP/ACCDZ, DFT/AUGCCPVDZ, B3LYP/AUGCCPVDZ               | 0.1465 | 0.6003 | 0.8761 | 0.0500 | 
| DFT/POBTZ, B3LYP/POBTZ                                               | 0.1300 | 0.4792 | 1.3962 | 1.3743 |
| *Special Cases*                                                                                          |
| GGA/MINIS, BLYP/MINIS,                                               | 0.1566 | 1.0732 | 1.1968 | 1.0271 |
| GGA/SVP, BLYP/SVP                                                    | 0.6823 | 0.8225 | 1.2811 | 1.2491 |
| GGA/SV, BLYP/SV                                                      | 0.2727 | 0.8055 | 1.3000 | 1.4022 |
| GGA/TZ, BLYP/TZ                                                      | 0.1182 | 1.0510 | 1.1287 | 1.0631 |
| TPSS/MINIS                                                           | 0.22982| 1.47633| 1.11300| 1.35401|
| TPSS/SVP                                                             | 0.6647 | 1.0792 | 1.1651 | 1.3306 |
| PW6B95/MINIS                                                         | 0.21054| 1.35003| 1.14061| 1.25458|
| PW6B95/SVP                                                           | 0.3098 | 0.6896 | 1.3347 | 1.2373 |
| PBE/DEF2SV(P)                                                        | 0.2424 | 0.6076 | 1.4078 | 1.2371 |
| PBEH3C                                                               | 1.00000| 0.27649| 1.95600| 1.32492|
| HSE3C                                                                | 1.00000| 0.28314| 1.94527| 1.32378|
| B3PBE3C                                                              | 1.00000| 0.3011 | 2.4405 | 2.98561|
| *No Data*                                                                                                | 
| RSH2C/MODDZ, B973C                                                   | -      | -      | -      | -      |

(\*): Available in CRYSTAL17 but not included in the script 'gCP.f90'. Their parameters are guessed from similar cases. 

B97-3c method doesn't adopt a standard gCP correction because of the relatively complete basis set. See the `B973C` keyword in the manual. 

Note that there are some options included in 'gCP.f90' but not in CRYSTAL17, which are not listed here. 