---
title: Names and Definitions of Popular Basis Sets
date: 2022-01-29 12:05:52 +0000
categories: [Research, DFT]
tags: [Atomic Basis Set, regular inspection]
math: true
---
In this post are listed the naming schemes of popular basis sets (BSs) that can be found in [Basis Set Exchange program](https://www.basissetexchange.org/) and [CRYSTAL basis set webpage](https://www.crystal.unito.it/basis-sets.php). Basis sets developed for pseudopotentials are not included in this post, since CRYSTAL code is more commonly used for more accurate all-electron calculations. The correctness of this page are regularly inspected. 

References:  
1. D. C. Young, in *Computational Chemistry: A Practical Guide for Applying Techniques to Real-World Problems*, Wiley, 2001, pp. 78–91.  
2. B. Nagy and F. Jensen, in *Reviews in Computational Chemistry*, John Wiley & Sons, Inc., Hoboken, NJ, USA, 2017, vol. 3, pp. 93–149.  
3. Sobereva, *谈谈弥散函数和“月份”基组*, <http://sobereva.com/119>. 

# Background knowledge
## The developing proposes of basis set
Basis sets in quantum chemistry are developed for different proposes, and therefore different optimization targets are adopted.

For BSs developed for the **exact electron correlation energy** of wave function methods, such as the coupled cluster method (CCSD, CCSD(T)), the target system is typically isolated atoms.

For BSs developed for **HF / DFT**, the target system for the occupied orbitals is isolated atoms. The target system for the polarization orbitals is usually molecules. 

## Contraction
The Gaussian functions used for constructing basis sets are called primitive functions (PGTO), which usually contains more functions than practically needed (contracted Gaussian type orbitals, CGTO). Since the core orbitals are insensitive to different chemical environments, it will be computationally efficient to represent the core shells with a fixed linear combination of Gaussian orbitals, which is known as *contraction*. 

**Segmented contraction**

In segmented contraction, every primitive function contributes to only a specific contracted function. Suppose 10 PGTOs are contracted to obtain 3 CGTOs: 

$$ \kappa_{1}=\sum^{6}_{\alpha=1}d_{\alpha}\chi_{\alpha} $$

$$ \kappa_{2}=\sum^{9}_{\alpha=7}d_{\alpha}\chi_{\alpha} $$

$$ \kappa_{3}=d_{10}\chi_{10} $$

**General contraction**

In general contraction, every CGTO is the linear combination of all PGTOs. 

$$ \kappa_{n}=\sum^{10}_{\alpha=1}d_{n,\alpha}\chi_{\alpha}, n=1,2,3 $$

In practice, the situation is usually in the middle. For example, several PGTOs with significant influence might repeat in segmented contraction basis sets, or PGTOs with negligible influences on a certain CGTO might be truncated in general contraction basis sets. The contraction method of the majority determines the contraction type. 

It is intuitively correct that segmented contraction basis set is computationally more efficient. However, it also requires the simultaneous optimization of the exponents and contraction coefficients, making basis set construction more difficult. 

## Split-valance: n-$$ \zeta $$ BSs
The distribution of valance electrons is susceptible to chemical environment. To add some flexibility, the valance shell can be expressed as the linear combination of multiple shells composed of GTOs with different parameters. *n*-$$ \zeta $$ indicates the valance shell is split into *n* shells. 

For HF/DFT calculations BSs with *n* beyond 4 or 5 can be regarded as close to the complete basis set (CBS) limit, which means problems related to basis set incompleteness are eliminated. 

## Augmentation: Polarization, diffuse, and tight

**Polarization functions**

To improve the flexibility of orbitals, another option is to add orbitals with higher angular momentum (in a new shell), and thus the orbitals allows angular distortions. 

**Diffuse functions**

The extended tail of Slater type orbitals (STOs) are poorly represented by GTOs. Therefore to improve the radical flexibility, diffuse functions with small exponential terms are inserted to the valance shells and sometimes also the polarization shells. 

**Tight functions**

Very accurate electron correlation calculations also require the angular flexibility for the contracted core orbitals, otherwise the their contributions are cancelled. Tight functions with large exponential terms are defined to fill the gap between the contracted core and the flexible valance orbitals. 

# Pople's basis sets

`STO-nG` - The minimal basis set. *n* GTOs are used to fit an STO. 

For other Pople BSs, they are segmented contracted, and HF (2-$$ \zeta $$) or MP2 (3-$$ \zeta $$) optimized. Only the valance shell can be augmented with diffuse functions. In Pople's BSs, except 1*s*, *s* and *p* orbitals are defined as *sp* orbitals to improve the efficiency. The naming scheme below is followed: 

`3-21G` - The double $$ \zeta $$ BS with segmented contraction, which consists of: 3 GTOs for cores, 2 GTOs and 1 GTO respectively for split valance shells. 

`6-21G*` - The double $$ \zeta $$ BS consists of 6 core GTOs, 2 + 1 valance GTOs, and 1 *d* polarization orbital (except H). 

`6-311G**` - The triple $$ \zeta $$ BS with 6 core GTOs, 3 + 1 + 1 valance GTOs and 1 *d* (or *p* for H) polarization orbital. 

`6-31+G` - The `6-31G` BS with diffuse functions for non-hydrogen elements. 

`6-31++G` - The `6-31G` BS with diffuse functions for all elements. 

`6-311G(2df)` - The `6-311G` BS with 2 extra sets of *d* and an extra set of *f* polarization orbitals. No polarization for H. 

`6-311++G(3df,3pd)` - The `6-311G` BS with diffuse functions at valance shell for all elements and 3 *d* + 1 *f* polarization orbitals for heavy atoms, 3 *p* + 1 *d* polarization orbitals for H. 

# Karlsruhe basis sets
Segmented contraction BS optimized by HF energy (occupied orbitals) or from cc-PVXZ (polarization orbitals). Parameters for electron correlation calculations are also available. Only the BSs for HF/DFT are discussed. 

## def2 version

`def2-[S/TZ/QZ]VP` - Karlsruhe def2- double/triple/quadruple $$ \zeta $$ split-valance-shell BSs, with polarization. 

## pob version

`pob-TZVP` - 

`pob-[DZ/TZ]VP-rev2` - 