This repository contains the numerical data underlying the reported results and figures. Source code and simulation implementations are not included.

# multi-target-photonic-splitter-data
Data underlying the results of a low-dimensional geometric design method for multi-target photonic power splitters, with a reusable MLP forward model for rapid design optimization.


# Inverse-Designed Optical Power Splitter Data

This directory contains the forward-model training, proposed-design, FDFD adjoint-reference, and broadband results for 1x2, 1x3, and 1x4 optical power splitters.

## Directory overview

```text
data/
├─ 2ports/
├─ 3ports/
└─ 4ports/
```

Each device directory is organized into four sections:

```text
training/   Forward-model training data and training results
proposed_geometry/   Proposed design results and FDFD verification
pixelwise_adjoint/   Pixel-wise FDFD adjoint-reference results
broadband/  Wavelength-dependent FDFD results from 1500 to 1600 nm
```

## Final training-data configurations

| Device | Final training samples | Composition | Fixed test samples | Training-data stages |
| --- | ---: | --- | ---: | --- |
| 1x2 | 300 | 50 initial + 250 additional | 50 | 50, 150, 250, 300 |
| 1x3 | 900 | 50 initial + 850 additional | 50 | 500, 600, 700, 800, 900 |
| 1x4 | 1800 | 50 initial + 1750 additional | 50 | 800, 1000, 1200, 1400, 1600, 1800 |

The fixed test samples are separate and are not included in the reported number of training samples. Short notes in each `proposed_geometry/proposed_result_notes_*.txt` file identify the training-data size used for the final proposed-design results.

## Training data

The `training/` directories contain:

- the initial FDFD-generated dataset;
- additional FDFD-generated training samples;
- the MLP training history;
- train/test predictions; and
- plots of the training loss and FDFD dataset distribution.

The additional training-data files are:

```text
2ports/training/extra_training_1x2_250.csv
3ports/training/extra_training_1x3_850.csv
4ports/training/extra_training_1x4_1750.csv
```

## Proposed-design results

The `proposed_geometry/` directories contain the proposed low-dimensional geometric-design results. A trained MLP forward model is used as a differentiable surrogate during gradient-based design optimization, and selected candidates are reevaluated by FDFD.

### Final candidates

```text
best_geometry/
```

This directory contains the final FDFD-selected candidate summary and the corresponding geometry, Poynting-magnitude, and output-transmission figures.

```text
candidate/
```

This directory contains:

- `proposed_design_candidates_T*.csv`: candidates ranked using the MLP surrogate;
- `proposed_fdfd_verified_candidates_T*.csv`: candidates reevaluated using FDFD.

### Training-data convergence

```text
proposed_by_training_samples/train_*/
```

Each stage directory contains the aggregate proposed-design convergence data and the final candidate summary obtained using that number of training samples. The maximum-sample directory contains the data corresponding to the final proposed-design plots:

```text
2ports: train_300/
3ports: train_900/
4ports: train_1800/
```

The PNG files named `proposed_optimization_history_T*.png` in each `proposed_geometry/` directory show the final convergence results. Their corresponding aggregate CSV files are stored in the maximum-sample directory listed above.

The aggregate convergence CSV files contain iteration-wise statistics across the inverse-design starts:

- `loss_best`: minimum loss;
- `loss_median`: median loss;
- `loss_mean`: mean loss;
- `loss_worst`: maximum loss.

### Candidate-level history policy

For the 1x2 device, `proposed_optimization_history_starts_T*.csv` files are included. These files contain the iteration history of every proposed-design optimization start and support candidate-level analysis.

For the 1x3 and 1x4 devices, the candidate-level `starts` files are omitted because of their substantially larger storage requirements. Their iteration-wise minimum, median, mean, and maximum losses remain available in `proposed_optimization_history_T*.csv`.

## FDFD adjoint-reference results

The `pixelwise_adjoint/` directories contain the pixel-wise density-based FDFD adjoint reference results. In all figures and display-oriented tables, **adjoint before thresholding** denotes the final projected density distribution, while **adjoint after thresholding** denotes the binary structure obtained by applying a hard threshold of 0.5. Both results come from the same adjoint optimization and include the differentiable projection used during optimization; the labels distinguish only the final representation used for FDFD evaluation:

- `T*_adjoint_history.csv`: iteration-wise projected and hard-thresholded adjoint-reference results;
- `T*_adjoint_history.png`: adjoint convergence plots;
- `adjoint_*_summary.csv`: final results for all target splitting ratios;
- `adjoint_power_validation_*.csv`, where provided: port-power validation results.

The `pixelwise_adjoint/final/` directories contain:

- `T*_adjoint_arrays.npz`: projected and hard-thresholded adjoint geometry, electromagnetic fields, and Poynting-vector arrays;
- `T*_adjoint_final.png`: final adjoint-reference figures.

The adjoint array archives include quantities such as `epsr`, `rho`, `Ez`, `Hx`, `Hy`, `Sx`, `Sy`, and `Smag` for the projected and hard-thresholded designs. Legacy internal field names containing `continuous` or `binary` are retained for compatibility with the original simulation outputs.

## Broadband results

The `broadband/` directories contain FDFD results evaluated from 1500 to 1600 nm using 51 wavelength points:

- `broadband_*_1500_1600_51pt_metrics.csv`: wavelength-dependent raw metrics;
- `*_broadband_normalized.csv`: normalized results used for comparison plots;
- `*_broadband_summary.csv`: target- and method-level summary;
- `*_broadband_response.png`: target-specific broadband plots;
- `*_integrated_report.png`: integrated device-level report.

## Target naming

Target names encode the requested output power ratio. Examples:

```text
T050_050             -> 0.50 : 0.50
T050_030_020         -> 0.50 : 0.30 : 0.20
T050_020_020_010     -> 0.50 : 0.20 : 0.20 : 0.10
```

## Plot-to-data mapping

| Plot or result | Primary data |
| --- | --- |
| MLP training loss | `training/training_history*.csv` |
| Train/test prediction analysis | `training/train_test_predictions*.csv` |
| Proposed-design convergence | `proposed_geometry/proposed_by_training_samples/train_*/proposed_optimization_history_T*.csv` |
| Candidate-level convergence, 1x2 only | `proposed_optimization_history_starts_T*.csv` |
| Training-sample comparison | `proposed_geometry/training_data_convergence_*.csv` and the stage directories |
| Final proposed geometry and transmission | `proposed_geometry/best_geometry/proposed_all_targets_best_summary*.csv` |
| Proposed-candidate FDFD verification | `proposed_geometry/candidate/proposed_fdfd_verified_candidates_T*.csv` |
| FDFD adjoint convergence | `pixelwise_adjoint/T*_adjoint_history.csv` |
| Projected and hard-thresholded adjoint geometry and fields | `pixelwise_adjoint/final/T*_adjoint_arrays.npz` |
| Broadband response | `broadband/broadband_*_metrics.csv` and `*_broadband_normalized.csv` |

## Notes on result verification

- CSV files contain the numerical data used for the corresponding plots.
- PNG files are included to make the reported results directly viewable.
- NPZ files preserve the adjoint-reference geometry and field arrays for additional plotting and analysis.
- The final proposed geometry is defined by the candidate coordinates in the CSV files and the geometry-generation implementation; proposed-design field arrays were not saved separately as NPZ files.


