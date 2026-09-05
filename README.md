# HD-sEMG Motor Unit Analysis and Cross-Angle Tracking

This repository contains a reproducible Jupyter workflow for HD-sEMG motor-unit analysis, including before-and-after manual-editing comparison, MUAP extraction, and motor-unit tracking across joint angles.

## What the notebook does

The workflow:

1. Loads original and manually edited MATLAB decomposition files.
2. Matches motor units before and after editing using full rate of agreement (RoA) and one-to-one Hungarian assignment.
3. Quantifies discharge rate, coefficient of variation, pulse-to-noise ratio, silhouette score, spike count, and added/deleted discharges.
4. Preprocesses HD-sEMG and extracts centered 25-ms motor-unit action potentials (MUAPs).
5. Tracks motor units across joint angles using grid-search assignment, normalized mean squared error (NMSE), and IQR-selected channels.
6. Produces tracking tables, arrow plots, and validation plots comparing accepted links with alternative matches.

For tracking validation, `Link` is the NMSE of the accepted adjacent-angle match. `Second best` is the minimum finite NMSE after excluding the accepted target MU, and `Mean alternatives` is the mean of all remaining finite candidate NMSE values in the destination angle.

## Input structure

Set `DATA_ROOT` in the notebook to a directory with this structure:

```text
Report_Analysis/
├── forearm_muedit_new/
│   └── <subject>/
│       ├── <recording>.mat
│       └── <recording>.mat_edited.mat
└── wrist_muedit_new/
    └── <subject>/
        ├── <recording>.mat
        └── <recording>.mat_edited.mat
```

Each edited MATLAB file must contain:

- `signal`: EMG `data` and sampling frequency `fs` or `fsamp`.
- `edition`: `Distimeclean` or `Dischargetimes`, plus `channelmap`.
- `edition/badchannel` is optional.

The matching raw `.mat` file is optional. When present, it is used as the pre-edit source; otherwise, the workflow uses the `signal` group in the edited file.

## Installation

Create the core environment:

```bash
conda env create -f environment.yml
conda activate emg-motor-unit-qc
jupyter notebook
```

The workflow also requires the research packages `motor_unit_toolbox` and `emg_toolbox`. Install or add those packages to the environment before running the notebook.

## Usage

1. Open `emg_motor_unit_qc_tracking.ipynb`.
2. Set `DATA_ROOT` to the input directory shown above.
3. Set `RESULT_ROOT` to a writable output directory.
4. Set `RUN_METRICS` and `RUN_TRACKING` in the final cell.
5. Run the notebook from top to bottom.

## Outputs

For each subject and recording location, the notebook creates:

- Per-MU and per-file analysis tables in CSV and Excel format.
- Metric-distribution figures.
- Compressed MUAP arrays in NPZ format.
- Cross-angle tracking chains and link-level NMSE tables.
- `tracking_arrows_*.png` and `.svg` visualizations.
- A tracking-validation figure comparing accepted links with alternative matches.

Global tables and figures are written under `_global/` in the selected result directory.

## Data privacy

Participant data, MATLAB input files, extracted MUAP arrays, and generated results are excluded by `.gitignore` and are not included in this repository.
