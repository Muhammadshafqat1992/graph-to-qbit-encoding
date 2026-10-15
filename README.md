# Data

The study uses three public MoleculeNet data sets (Wu et al., *Chem. Sci.* 2018, 9, 513-530). The notebook downloads them from the DeepChem repository, so they are not copied here.

| Data set | Task | Source file |
|---|---|---|
| BBBP | blood-brain barrier penetration (binary classification) | https://deepchemdata.s3-us-west-1.amazonaws.com/datasets/BBBP.csv |
| BACE | inhibition of human beta-secretase 1 (binary classification) | https://deepchemdata.s3-us-west-1.amazonaws.com/datasets/bace.csv |
| ESOL | aqueous solubility in log mol/L (regression) | https://deepchemdata.s3-us-west-1.amazonaws.com/datasets/delaney-processed.csv |

## Cleaning

The notebook parses every SMILES string with RDKit and converts it to its isomeric canonical form. It then:

1. removes records that cannot be parsed or have a missing or non-numeric target;
2. merges records that describe the same canonical structure with the same target;
3. removes structures that appear with conflicting targets.

| Data set | Raw | Parsed | Duplicates | Conflicts | Final | Scaffolds |
|---|---|---|---|---|---|---|
| BBBP | 2,050 | 2,039 | 64 | 10 | 1,965 | 1,166 |
| BACE | 1,513 | 1,513 | 0 | 0 | 1,513 | 743 |
| ESOL | 1,128 | 1,128 | 11 | 6 | 1,111 | 367 |

The molecules are grouped by Bemis-Murcko scaffold. The notebook builds three outer folds in which no scaffold group is split, and checks that the training, validation and test partitions share no scaffold and no canonical SMILES.
