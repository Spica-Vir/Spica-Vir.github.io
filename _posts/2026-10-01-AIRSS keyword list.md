---
title: AIRSS keyword list
date: 2026-10-01 11:47:00 +0800
categories: [Research, others]
tags: [structure search, AIRSS]
math: true
---

The official documentation site of [AIRSS](https://airss-docs.github.io/) only offers an incomplete collection of keywords.
Therefore, with the help of CodeX 5.6 Sol, a complete keyword list is reported in this post, which was traced from source code of the 'buildcell' executable of AIRSS 0.9.4, the latest version by Oct. 2026.
Also thanks to Mr F. Lei for offering an overview of AIRSS-EDDP.

## Sources

The following public sources were used to clarify intended usage and terminology.

- [AIRSS buildcell manual](https://airss-docs.github.io/technical-reference/buildcell-manual/)
- [AIRSS examples](https://airss-docs.github.io/tutorials/examples/)

## About AIRSS

The introduction of AIRSS is available on its [documentation site](https://airss-docs.github.io/).
Among various executables and scripts of AIRSS package, the `buildcell` utility plays a central role in random structure generation.
In short, `buildcell` acts as the structure generation engine, which reads the input setups and generates *one* sensible structure, while the upper-layer `airss.pl` manages the massive and parallel generation process by repeatedly calling `buildcell`, as well as solvers (CASTEP, for example) behaviors.

`buildcell` reads CASTEP-formatted input file (`.cell`) with additional keywords.
The compatibility with CASTEP I/O is ensured following such convention:

| Input      | CASTEP        | AIRSS                                                             |
| :--------- | :------------ | :---------------------------------------------------------------- |
| KEYWORD    | Valid keyword | CASTEP keyword, geometry-related keywords are read                |
| # KEYWORD  | Comment       | Valid `buildcell` keyword                                         |
| ## KEYWORD | Comment       | Comment                                                           |
| ! KEYWORD  | Comment       | Unrecognized, can lead to interpreting issues within input blocks |

The input keywords for `buildcell` are listed below.
Instructions for the command line options of `airss.pl` are available via `airss.pl -h`, which are omitted here.

## Modelling

| Keyword     | Meaning                                                        | Allowed values         | Default    |
| :---------- | :------------------------------------------------------------- | :--------------------: | :--------: |
| `SUPERCELL` | Supercell expansion                                            | 1 / 3 / 9 ints         | 1          |
| `TIGHT`     | Allow for more compact structure for initial separation checks |                        | (disabled) |
| `SPIN`      | Set the mean net spin and absolute spin **per atom**           | net absolute <species> | 0 0        |
| `COMPACT`   | Niggli reduction for the final cell                            |                        | (enabled for unconstrained cells only) |
| `NOCOMPACT` | Disable Niggli reduction for the final cell                    |                        | (disabled) |
| `FLIP`      | Multi-atom units have a 50% chance of being mirrored (for chirality) |              | (disabled) |

### Variable compositions and defects

| Keyword     | Meaning                                                            | Allowed values                           | Default                  |
| :---------- | :----------------------------------------------------------------- | :--------------------------------------: | :----------------------: |
| `NATOM`     | Total number of atoms when `SPECIES` is used                       | Given or a range of positive ints        | (implied by `SPECIES`)   |
| `SPECIES`   | Atoms from a comma-separated species list                          | Element with optional `%NUM=<int/range>` | (disabled)               |
| `NFORM`     | Number of formula units of the input composition                   | Given or a range of positive ints        | (determined by symmetry) |
| `FOCUS`     | Search for structures with specified distinct species only         | Positive int                             | (disabled)               |
| `VACANCIES` | Randomly remove specified number of atoms of the specified species | `<Positive int>@<species>`               | (disabled)               |
| `PERMUTE`   | Randomly permute species among atomic positions                    | No input or `#PERMUTE=<species>`         | (disabled)               |
| `PERMFRAC`  | Fraction of shuffled specifies `A` when `#PERMUTE=A`               | 0 -- 1                                   | 1 if `PERMUTE` enabled   |


### Isolated systems

| Keyword     | Meaning                                                                 | Allowed values | Default    |
| :---------- | :---------------------------------------------------------------------- | :------------: | :--------: |
| `CLUSTER`   | Non-periodic atomic cluster and geometry handling based on point group  |                | (disabled) |
| `SLAB`      | z-symmetric slab with layer operations                            |                | (disabled) |
| `SURFACE`   | z-asymmetric slab with layer operations                           |                | (disabled) |
| `VACUUM`    | Vacuum padding along z for crystal or 3-dimensions for cluster with `SPHERE` | Positive real in $\AA$ | 0  |


## Constraints for structural perturbations

### Lattice constraints

| Keyword   | Meaning                                                                               | Allowed values                    | Default         |
| :-------- | :------------------------------------------------------------------------------------ | :-------------------------------: | :-------------: |
| `SYSTEM`  | Crystal system constraint, conflicts with `CELLCON`                                   | Tric, Mono, Hexa, Rhom, Trig, Orth, Tetr, Cubi                  | (unconstrained) |
| `CELLCON` | Lattice parameter constraint vector, conflicts with `SYSTEM`                          | 0 (free), positive real in deg (angle), -1 (equivalent lengths) | (unconstrained) |
| `ABFIX`   | Fix lattice vectors a, b, inside the `LATTICE_*` block                                |                                   | (unconstrained) |
| `CFIX`    | Fix the lattice vector c, inside the `LATTICE_*` block                                |                                   | (unconstrained) |
| `FIX`     | Fix the cell, inside the `LATTICE_*` block                                            |                                   | (unconstrained) |
| `CONS`    | Min cell aspect ratio constraint (see the note below)                                 | 0 (free) -- 1 (equiaxial)         | 0.4             |
| `ACONS`   | Min normalized volume factor constraint (see the note below)                          | 0 (free) -- 1 (orthogonal)        | 0.5             |
| `CELLAMP` | Max fractional amplitude to perturb the current cell for new cells                    | 0 -- 1                            | (from scratch)  |
| `TARGVOL` | Volume constraint, scaled for atom counts                                             | Given or a range of positive real | (from cell matrix or estimate from atomic radii) |
| `VARVOL`  | Center of volume constraint range, with variation of 95%-105%, scaled for atom counts | Positive real                     | (disabled)                   |

**Notes**

1. The actual minimum aspect ratio is implied as $CONS/(2-CONS)$, applied to all three axes and six facets.
2. The normalized volume factor is:

$$f=\frac{V}{abc} =\sqrt{1-\cos^2\alpha-\cos^2\beta-\cos^2\gamma+2\cos\alpha\cos\beta\cos\gamma}$$

$f=1$ for orthogonal cell; $f\rightarrow0$ for flat cell.

### Internal coordinate constraints

| Keyword     | Meaning                                                                                      | Allowed values                   | Default             |
| :---------- | :------------------------------------------------------------------------------------------- | :------------------------------: | :-----------------: |
| `COORD`     | Atom coordination constraint, outside (overall) or inside (per unit) the `POSITIONS_*` block | Given or a range of positive int | (unconstrained)     |
| `MAXBANGLE` | Max bond angle when `COORD` is active                                                        | 0 -- 180                         | 180                 |
| `MINBANGLE` | Min bond angle when `COORD` is active                                                        | 0 -- 180                         | 0                   |
| `MINSEP`    | Min interatomic distance                                                                     | See below                        | Sum of atomic radii |
| `RAD`       | Hard atomic radii, outside (overall) or inside (per unit) the `POSITIONS_*` block            | Positive real in $\AA$           | (separation-based)  |
| `SLACK`     | Relax interatomic distance and bond angle constraints by fraction                            | 0 -- 1                           | 0                   |
| `AUTOSLACK` | Increase `SLACK` stepwise when cell is re-initialized (due to overly tight constraints)      | 0 -- 1                           | (disabled)                   |
| `MOLECULES` | Constrain intramolecular configurations for molecular crystals/clusters, disabled for the current version | (disabled)          | (disabled)          |
| `OCTET`     | Valence-electron count must be a multiple of eight (octet rule)                              |                                  | (disabled)          |
| `POSAMP`    | Max radial displacement, outside (overall from geometrical center) or inside (per unit from initial position) the `POSITIONS_*` block               | Positive real in $\AA$ | (unconstrained) |
| `MINAMP`    | Min radial displacement, outside (overall from geometrical center) or inside (per unit from initial position) the `POSITIONS_*` block               | Positive real in $\AA$ | 0               |
| `XAMP`      | $[-XAMP/2, XAMP/2]$ around the center, outside (overall from geometrical center) or inside (per unit from initial position) the `POSITIONS_*` block | Positive real in $\AA$ | (unconstrained) |
| `YAMP`      | $[-YAMP/2, YAMP/2]$ around the center, outside (overall from geometrical center) or inside (per unit from initial position) the `POSITIONS_*` block | Positive real in $\AA$ | (unconstrained) |
| `ZAMP`      | $[-ZAMP/2, ZAMP/2]$ around the center, outside (overall from geometrical center) or inside (per unit from initial position) the `POSITIONS_*` block | Positive real in $\AA$ | (unconstrained) |
| `ANGAMP`    | Max rotation, outside (overall from geometrical center) or inside (per unit from initial position) the `POSITIONS_*` block                          | Positive real in deg   | (unconstrained) |
| `CYLINDER`  | Cylindrical confinement / potential along z                                                  | Positive real in $\AA$ for confinement；Negative real for strength | (unconstrained) |
| `SPHERE`    | Spherical confinement                                                                        | Positive real in $\AA$ for radius; Negative real for radius scaling with the total number of atoms N^(1/3) | (unconstrained) |
| `SHIFT`     | The center of slab confinement normal to z                                                   | Positive real in $\AA$           | 0                   |
| `WIDTH`     | The width of slab confinement normal to z                                                    | Positive real in $\AA$           | (disabled)          |


**Note**

Accepted values of `MINSEP`:
- Given or a range of positive real
- Specific element pairs, `#MINSEP=1.0 C-H=1.1 ` or `#MINSEP=1.0 C-H=1.0-1.2`
- `AUTO`, `AUTOVOL` estimated based on volume

### Symmetry constrains

| Keyword      | Meaning                                                                                 | Allowed values                             | Default    |
| :----------- | :-------------------------------------------------------------------------------------- | :--------------------------------------- : | :--------: |
| `SYMM`       | Space / point group constraint by HM / Schoenflies symbols, '~' enables approx matching | Group name                                 | P1 or C1   |
| `SYMMNO`     | Group symmetry constraint by number, '~' enables approx matching                        | Given, a range of, or comma-separated ints | 1          |
| `SYMMOPS`    | Total number of symmetry operations, '~' enables approx matching                        | 1,2,3,4,6,8,12,16,24,48 for space groups, 1,2,3,4,5,6,7,8,9,10,11,12,24 point groups, or ranges between allowed endpoints | (disabled) |
| `SYMMORPHIC` | Restrict the selected symmetries to symmorphic groups, with no fractional translations  |                                            | (disabled) |
| `SGRANK`     | Restrict the selected symmetry to the `SGRANK` most common space groups                 | Int 1 -- 230                               | 230        |
| `BREAKAMP`   | Max random displacement to break symmetry                                               | Positive real in $\AA$                     | (disabled) |
| `REMOVE`     | Remove overlapped atoms within 0.001 $\AA$, do not re-initialize the cell (for high-symmetry positions) |                            | (disabled) |
| `ADJGEN`     | Add symmetrically inequivalent sites, usually at high-symmetry positions                | Given or a range of ints                   | 0          |

## Computational control

| Keyword       | Meaning                                                                                     | Allowed values         | Default    |
| :------------ | :------------------------------------------------------------------------------------------ | :--------------------: | :--------: |
| `MAXTIME`     | Max CPU time per structure generation step                                                  | Positive real in s     | 1.0        |
| `NOPUSH`      | Disable the default push-apart step that maintains the distance and coordination constraint |                        | (disabled) |
| `OVERLAP`     | Enable the hard-sphere optimization and set the max atom overlap threshold                  | Positive real          | (disabled) |
| `CELLADAPT`   | Constant-volume optimization based on the hard-sphere potential, conflict with `FIX`        |                        | (disabled) |
| `THREE`       | Cutoff for the three-body term of hard-sphere potential, disabled for the current version   | (not allowed)          | (disabled) |
| `RASH`        | Enable the relax-and-shake algorithm for hard-sphere optimization                           |                        | (disabled) |
| `RASH_ANGAMP` | Max angular shake of `RASH`                                                                 | Positive real in deg   | 30         |
| `RASH_POSAMP` | Max translational shake of `RASH`                                                           | Positive real in $\AA$ | 1          |

### Notes for hard-sphere optimization

After random structures generated, AIRSS performs the separation check and utilizes the default push-apart method to meet geometry constraints. 
Then, optionally, the structure can be optimized with the hard-sphere potential to minimize the artificial total energy and interatomic forces for a geometrically reasonable candidate.

### Notes for RASH algorithm

The concept of RASH algorithm has been introduced in [AIRSS examples](https://airss-docs.github.io/tutorials/examples/), but at the 'buildcell' stage, RASH is performed based on hard-sphere potential, instead of physical-property calculators.
Therefore it should be regarded as an alternative technique to minimize the artificial energy.

## Unavailable or potentially buggy keywords

For the latest AIRSS 0.9.4, some keywords in the list are disabled or can be potentially buggy without specific formatting, which are collected below.

- `MOLECULES`: Disabled.
- `OCTET`: Disabled. Calling it terminates the program with `valence not known`.
- `THREE`: Disabled. Calling it terminates the program.
- `ADJGEN`: Using a range of ints can be buggy. The lower bound is read from the wrong variable, use with caution.
- `NOPUSH`: Case-sensitive. Use exactly upper cases.
- `MINSEP`: A global value must be given prior to pairwise overriden.
