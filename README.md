# DRY-MARTINI PEG Force Field

DRY-MARTINI-PEG is an extension to the DRY-MARTINI force field for simulating
Pegylated lipids, alkyl ethoxy surfactants and PEG and PEO polymers.
It is an implicit water coarse-grained molecular dynamics (CG-MD) force field.

If you want to use this model, please cite [My Doctoral Thesis](ISBN) for the implementation, and [Shape and Phase Transitions in a PEGylated Phospholipid System](https://pubs.acs.org/doi/10.1021/acs.langmuir.8b03829) for the model`s application. The base DRY-MARTINI citation is [here] (https://pubs.acs.org/doi/abs/10.1021/ct500477k).

If you spot any bugs or improvements, please  [raise an issue](https://github.com/jsmaatta/dry_martini_peg/issues). You can also add any starting structures etc.

If you want to contribute you can fork this repository and make a pull request for DRY-MARTINI-PEG at [pull requests](https://github.com/jsmaatta/dry_martini_peg/pulls).


# Quick start
Using this model is really easy, especially if you have any experience with MARTINI models.

##Step 1:

```
git clone https://github.com/jsmaatta/dry_martini_peg
```

##Step 2:
You have to include the following files for your topology:

```
#include "dry_martini_v2.1_peg.itp"   ; this includes the new PEG bead interactions
#include "dry_martini_v2.1_PEGsurfactants.itp"  ; this includes the new PEG bonded interactions and couple small surfactants
#include "dry_martini_v2.1_lipids.itp" ; standard DRY-MARTINI lipids 
#include "dry_martini_v2.1_ions.itp" ; standard DRY-MARTINI ions
#include "dry_martini_v2.1_solvents.itp"  ; standard DRY-MARTINI solvents  
; and optionally if simulating large PEGylated lipids
#include "dppe-peg45.itp"  ; big pegylated lipids have their own itp files
```

##Step 3:

We used both the standard DRY-MARTINI mdp file for simulations as well as the new
```
newrf.mdp
```
The advantage of the newrf mdp is that one can then use Gromacs GPU acceleration to make simulations much faster.

##Step 4:
We included couple different starting configurations
```
plpeg.gro
.gro
```
But also all standard martini PEG configurations should work too. Just remove the MARTINI waters and you're good to go.

Step 5:
Run the energy minimization and the md run itself.
```
gmx grompp -f em.mdp -c conf.gro -p topol.top -o em.tpr
gmx mdrun -v -deffnm em

and

gmx grompp -f newrf.mdp -c em.gro -p topol.top -o md.tpr
gmx mdrun -v -deffnm md
```

##
Happy simulating!

# Making a new PEG molecule
The new PEG model uses the normal MARTINI conventions except that the PEG beads are modeled with 3-to-1 mapping with small bead type, whereas most DRY-MARTINI beads are modeled with 4-to-1 mapping. A great care was taken in the parameterization so that one can still use the normal 40fs timestep from DRY-MARTINI.

We use the standard MARTINI procedure which means e.g. the bonds are defined with  `indices, gromacs bond type, gromacs  bond name convention` as in `1 2 1 mb_PEG` is the harmonic PEG bond type 1 between atoms 1 and 2.

The bond from PEG to a Carbon bead is defined with harmonic type `1 mb_CPEG`, the bond between PEG (or PEO) beads with `1 mb_PEG` and the bond to and end-capped Hydrogen with `1 mb_PEGh`.

However, we use the [combined bending-torsion potential](https://pubs.acs.org/doi/10.1021/ct400219n) for the PEG dihedral interactions. You can look at the files `dry_martini_v2.1_PEGsurfactants.itp` for definitions and `dppe-peg45.itp` for usage. 
That means that the dihedrals are defined with improved combined bending-torsion potential type `11 mdd_PEG` and angles for the dihedrals with standard harmonic `2 maa_PEG` types. So simply just use `mdd` for dihedrals and `maa` for angles for all PEG interactions. 

We recommend using the improved bending-torsion potential from above but if you are using a really old version of Gromacs or some other simulation package that does not support the improved angle potential, there is also restricted angle potential as an alternative. For that you should use, ryckaert bellemans type `3 md_PEG` for  the dihedrals, harmonic `2 ma_PEG` for angles and also add type `10 mr_PEG` restricted angle potential. The restricted angle potential parameters were parameterized to give almost identical results as the combined bending-torsion potential. 

You can combine the PEG/PEO chain with any DRY-MARTINI bead. The examples include `dppe-peg45.itp` where a 45 monomer PEG chain was attached to a DPPE lipid making a Pegylated DPPE and dry_martini_v2.1_PEGsurfactants.itp where 12 monomer PEO chain was attached to carbon chains of various length making C12E2, C12E4 and C12E6 alkyl ethoxy surfactants. Triblock Carbon-PEG-Carbon and other polymers are also possible to make very easily by just attaching the PEG chain to other groups. 


## Which simulation software?

Like most MARTINI simulations, the DRY-MARTINI PEG simulations performed with [GROMACS](www.gromacs.org),

# Papers with DRY-MARTINI PEG Force Field
+ [Shape and Phase Transitions in a PEGylated Phospholipid System](https://pubs.acs.org/doi/10.1021/acs.langmuir.8b03829)

 
## Notes

