# Topology-aware graph-to-qubit encoding for molecular property prediction

This repository contains the code for the manuscript

> M. Shafqat, M. Farooq, A. Ghafoor, A. Basit. *Topology-aware graph-to-qubit encoding for molecular property prediction.* Submitted to the Journal of Chemical Information and Modeling (2026).

The framework maps molecules of any size onto a fixed six-qubit register. A directed-bond message-passing network produces atom embeddings. A learned tokenizer then assigns the atoms to six qubit-level tokens, and three relation matrices between the tokens set the angles of the ZZ and XX entangling gates. The 27 measured values make a bounded correction to a classical prediction.

## Contents

| File | Description |
|---|---|
| `proposed_model_and_baselines.ipynb` | Data download and cleaning, scaffold-disjoint splits, the fingerprint MLP, the directed-bond GNN, the proposed model, the ablation variants, the paired and pooled statistics, the simulated noise study with zero-noise extrapolation, the circuit resource audit and the figures |
| `hybrid_qgnn_vqe_baseline.ipynb` | The reimplemented Hybrid QGNN-VQE baseline, trained on the same splits and seeds |
| `requirements.txt` | Python packages |
| `LICENSE` | MIT license |

Both notebooks are saved with their outputs, so the logs and result tables of every run can be read without running the code.

## Data

BBBP, BACE and ESOL are public data sets from the MoleculeNet benchmark collection (Wu et al., *Chem. Sci.* 2018, 9, 513-530). The first notebook downloads them directly from the DeepChem repository, so they are not copied here.

| Data set | Task | Source file |
|---|---|---|
| BBBP | blood-brain barrier penetration (binary classification) | https://deepchemdata.s3-us-west-1.amazonaws.com/datasets/BBBP.csv |
| BACE | inhibition of human beta-secretase 1 (binary classification) | https://deepchemdata.s3-us-west-1.amazonaws.com/datasets/bace.csv |
| ESOL | aqueous solubility in log mol/L (regression) | https://deepchemdata.s3-us-west-1.amazonaws.com/datasets/delaney-processed.csv |

No other data are used.

The notebook parses every SMILES string with RDKit and converts it to its isomeric canonical form. It then removes records that cannot be parsed or have a missing or non-numeric target, merges records that describe the same canonical structure with the same target, and removes structures that appear with conflicting targets.

| Data set | Raw | Parsed | Duplicates | Conflicts | Final | Scaffolds |
|---|---|---|---|---|---|---|
| BBBP | 2,050 | 2,039 | 64 | 10 | 1,965 | 1,166 |
| BACE | 1,513 | 1,513 | 0 | 0 | 1,513 | 743 |
| ESOL | 1,128 | 1,128 | 11 | 6 | 1,111 | 367 |

The molecules are grouped by Bemis-Murcko scaffold. The notebook builds three outer folds in which no scaffold group is split, and checks that the training, validation and test partitions share no scaffold and no canonical SMILES.

## Environment

The experiments were run on Kaggle with a GPU and Python 3.12. The main packages were:

- PyTorch 2.10 (CUDA)
- PennyLane 0.45.1
- RDKit (2024.3 or later)
- scikit-learn
- XGBoost 3.2
- NumPy, pandas, SciPy and matplotlib

All of these packages are free and open source. `pip install -r requirements.txt` installs them. The first cell of each notebook also installs any package that is missing.

## How to run

1. Run `proposed_model_and_baselines.ipynb` from top to bottom. It downloads the data, builds the splits, trains all models and writes the results to its working folder.
2. Run `hybrid_qgnn_vqe_baseline.ipynb`. It rebuilds the same splits with the same fixed seed and reads `all_job_metrics.csv` from the first notebook for the final comparison. If the first notebook was run in a different session, set `Config.source_handle` to the folder or Kaggle data set that holds its results.

The cells under "Persistent State" save and restore progress through a Kaggle data set, so that long runs can be resumed. They need Kaggle credentials and are not needed to reproduce the results. To switch them off, set `enable_persistent_sync = False` in the Configuration cell of the first notebook.

## Settings

- Three outer scaffold folds per data set, with a scaffold-disjoint validation split inside each training fold
- Model seeds 19 and 53, which gives six runs per model and data set
- Six qubits, two variational layers, and 30 two-qubit gates per molecule
- The noise study uses depolarising, phase-flip, phase-damping, amplitude-damping and readout noise at p = 0, 0.005, 0.01 and 0.02

## License

MIT License. See `LICENSE`.

## Contact

Muhammad Shafqat, National University of Sciences and Technology (NUST), Islamabad, Pakistan. muhammadshafqat1992@gmail.com
