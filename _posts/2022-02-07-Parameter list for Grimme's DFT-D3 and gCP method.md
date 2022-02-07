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

They show good readability so are not listed separately. 
