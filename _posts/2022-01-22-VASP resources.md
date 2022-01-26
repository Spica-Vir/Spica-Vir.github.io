---
title: VASP resources
date: 2022-01-22 11:05:20 +0000
categories: [Research, DFT]
tags: [software, VASP]
---

In this post are listed some useful learning resources of the (probably) most popular planar wave basis set ab initio simulation package, VASP. Due to its popularity, there are tones of resources available online. Here only a minor port of them are listed, which, as I found, particularly useful. This post is just a list of resources I collected during my 'VASP era' (19-21), so regular updates might be unavailable since I do not work on VASP any more. Resources checked Jan. 22-2022. 

# Official sites
[**VASP Wiki**](https://www.vasp.at/wiki/index.php/The_VASP_Manual) - The online VASP manuals, tutorials etc. Maintained and released by VASP developers, it is certainly the most reliable source to learn VASP. 

[**VASP forum**](https://www.vasp.at/forum/) - The official forum for VASP users. For most of times, VASP developers are very active in this forum - their replies can be found under many posts. 

The old website of VASP, <http://cms.mpi.univie.ac.at>, has been unfortunately abandoned since late 2020. Old forum posts are believed to be safely transferred to the new forum, yet the their original links are no longer valid. Besides, the retired [online manual](https://cms.mpi.univie.ac.at/vasp/vasp/vasp.html) is not available. The contents of the [PDF manual](http://cms.mpi.univie.ac.at/vasp/vasp.pdf) are the same as its online counterpart, so they are out-of-date as well. The only reliable manual for the current version of VASP is [VASP Wiki](https://www.vasp.at/wiki/index.php/The_VASP_Manual). 

[**List of resources**](https://www.vasp.at/resources/) - The officially recommended tool kits for VASP simulations, which, I think, is better than any other recommendation. 

# Other learning resources
[**Learn VASP The Hard Way (LVASPTHW)**](https://www.bigbrosci.com/) - *中文* 非常详细的初级到中高级教程，美中不足的就是稍显罗嗦。 

**侯柱峰VASP手册** - *中文* 入门级手册

涵盖了VASP编译，SCF，几何结构优化的参数测试方法、选择技巧，以及电学、磁学、界面性质计算的建模与计算方法。需要注意手册只有2004和2005两个版本，部分方法（如几何结构优化的方法）已经过时，需要结合[VASP Wiki](https://www.vasp.at/wiki/index.php/The_VASP_Manual)批判学习。

[**世事如棋的GitHub博客**](http://blog.wangruixing.cn/2019/05/01/scf/) - *中文* 一些关于VASP以及配套工具使用方法的收录，讨论非常深入但是系统性不强。

[**第一原理計算入門**](https://www5.hp-ez.com/hp/calculations/page1) - *Japanese* A collection of instructions for various ab initio codes. Focused on compiling / setting up VASP and related tools.

# Pseudopotentials
The pseudopotential files (POTCAR) for VASP are maintained and regularly updated by VASP developers. Sadly they are only available for licensed users from the [download portal](https://www.vasp.at/vasp-portal/login/?next=/vasp-portal/). Some 'unofficial' resources might be available, but constructing pseudopotentials is a delicate thing, so check the instructions & editions carefully before using. 

# Visualization
The VASP format is widely supported by various mainstream visualization software. 

[**VESTA**](http://jp-minerals.org/vesta/en/) - The visualization software supporting various geometry formats (but not CRYSTAL). Applicable for Windows, MacOS, and Linux.

Known Issue
: The old 3.4.x versions, except 3.4.0, get problems when visualizing the charge density file (CHGCAR). Not a problem for now since the latest version is 3.5.7. 

# Pre-processing & Computation
**vdW-DF** - Including `vdw_kernel.bindat` and `vdw_kernel.bindat.big_endian`binary files. 

The latter one is for big endian devices. Both are available for licensed users from the [download portal](https://www.vasp.at/vasp-portal/login/?next=/vasp-portal/). Unofficial copies might be available. It might be possible to automatically generate a new one by VASP during calculations, but the time consumption skyrockets, probably for days. Place it in the job folder and edit `INCAR` as [instructed](https://www.vasp.at/wiki/index.php/VdW-DF_functional_of_Langreth_and_Lundqvist_et_al.).

**constr_cell_relax.F** - Perform geometry optimizations along the specified direction(s) only. 

The script is stored in `src/` and is disabled. Refer to the comments in the script for instructions to activate this function. Here an example is provided: 

```FORTRAN
SUBROUTINE CONSTR_CELL_RELAX(FCELL)
USE PREC
REAL(Q) FCELL(3,3), SAVE(3)
LOGICAL FILFLG
INTEGER ICELL(3)
INQUIRE(FILE='OPTCELL',EXIST=FILFLG)
IF (FILFLG) THEN
    ! read in OPTCELL
    OPEN(67,FILE='OPTCELL',FORM='FORMATTED',STATUS='OLD')
    READ(67,"(3I1)") (ICELL(I),I=1,3)
    CLOSE(67)
    !WRITE(*,*) "NOTICE: OPTCELL read in !"

    DO I=1,3
        SAVE(I)=FCELL(I,I)
    ENDDO
    FCELL=0.0d0
    DO I=1,3
        IF(ICELL(I)==1) FCELL(I,I)=SAVE(I)
    ENDDO
ENDIF
RETURN
END SUBROUTINE
```

Place the script into `src/` and re-compile VASP5.4. A new file, `OPTCELL` should be placed into the job folder. `OPTCELL` is a 3x3 integer, binary matrix (`ICELL(3,3)`). `0` denotes fixing the corresponding element of the cell matrix (`FCELL`) in `POSCAR`, while `1` means relax it. 

Update Jan. 22-2022
: I recently got a copy of VASP6.1.2 source codes (via a private way), the situation remains unchanged. 

[**VTSTTools**](https://theory.cm.utexas.edu/vtsttools/index.html) - For transition state search and reactions. Not really used. 

[**Bader**](http://theory.cm.utexas.edu/henkelman/code/bader/) - For atomic Bader charge analysis. Used with `chgsum.pl` script from VTSTTools. 

Although from a personal point of view, population analysis is somewhat 'nonphysical', and in many cases the results of Bader charge is even worse than the simpler Mulliken charge, but to the best of my knowledge, Bader analysis is the only available option for VASP. 

[**phonopy**](https://phonopy.github.io/phonopy/) and [**phono3py**](http://phonopy.github.io/phono3py/) - Typically for phonon dispersion and thermodynamic properties via finite displacement method. 

The formal one is written in Python 2 and the latter one in Python 3 (should be, I did not remember clearly. Generally speaking they are the same in features). Combined with VASP to analyze the symmetry of input geometry and displace the initial geometry along all the symmetrically nonequivalent directions for phonon calculations. It also has interface to other calculators including CRYSTAL. 

[**Raman-sc**](https://github.com/raman-sc/VASP) - A Python 2 package for Raman spectra simulation - not a mature edition.

According to my preliminary tests only peak positions can be calculated correctly, nevertheless still an impressive achievement considering the lack of support of VASP in vibrational properties. 

# Post-processing
[**p4vasp**](https://github.com/orest-d/p4vasp) - A powerful post-processing software with graphical user interface

It can be used to read, visualize, and export various electronic properties. Applicable for Windows, MacOS, and Linux. Based on `vasprun.xml` file, it can be launched by simply copying its executable file to the job folder. It can also used for structure visualization but not as beautiful as [VESTA](http://jp-minerals.org/vesta/en/). 

Known issue
: Its [information webpage](http://www.p4vasp.at/) has been down due to sever problems since late 2021. Only [GitHub page](https://github.com/orest-d/p4vasp) is available now. 

[**VASPKIT**](http://vaspkit.sourceforge.net/) - A light-weight tool kit for post-processing. 

No graphical interface, only available on Linux. Capable of dealing electronic and optic properties including band, DOS, electrostatic potential, differential charge/spin density and planar averaged properties. 