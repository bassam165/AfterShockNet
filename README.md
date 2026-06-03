# AfterShockNet

**Hybrid Operational Aftershock Forecasting with Spatio-Temporal Graph Neural Networks, Transformers, and ETAS Using Global Seismic Catalogs and Geophysical Constraints**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.9+](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/)
[![PyTorch 2.2](https://img.shields.io/badge/PyTorch-2.2.2-red.svg)](https://pytorch.org/)

---

## Overview

AfterShockNet is a global hybrid aftershock forecasting framework that integrates the **ETAS statistical model**, **GATv2 spatio-temporal graph attention networks**, and a **multi-horizon Transformer encoder** within a unified end-to-end trainable architecture.

The framework simultaneously forecasts:

- Aftershock **occurrence probability**
- Expected **maximum magnitude**
- **Geographic centroid** location

Across five forecast horizons: **1h · 6h · 12h · 24h · 72h**

---

## Results

Evaluated on **1,037,060 global seismic events (1900–2026)**.

| Horizon | F1    | PR-AUC | Mag-MAE | Spatial Error |
|:-------:|:-----:|:------:|:-------:|:-------------:|
| 1h      | 0.588 | 0.701  | 0.402   | 5,021 km      |
| 6h      | 0.652 | 0.752  | 0.407   | 3,572 km      |
| 12h     | 0.682 | 0.779  | 0.408   | 3,207 km      |
| 24h     | 0.710 | 0.821  | 0.413   | 2,921 km      |
| 72h     | 0.731 | 0.882  | 0.414   | 2,699 km      |

Wilcoxon signed-rank tests confirm **p < 0.001** superiority over all baselines (ETAS, XGBoost, LSTM, Transformer).

### Ablation Study

| Removed Component         | 1h F1 | Drop   |
|---------------------------|:-----:|:------:|
| Full AfterShockNet        | 0.588 | —      |
| w/o ETAS Prior Embedding  | 0.233 | −60.4% |
| w/o GNN Encoder           | 0.295 | −49.8% |
| w/o Transformer Encoder   | 0.239 | −59.4% |
| Single Window Only (w24)  | 0.303 | −48.5% |

---

## Architecture

```
Input (16-dim node features per seismic event)
    |
    v
ETAS Prior Embedding Module (5 -> 32 dim)
    |
    v
Physics-Augmented Node Projection (48 -> 128 dim)
    |
    v
Three Parallel Lookback Windows: 24h · 48h · 72h
    Each: 3 x GATv2 Layers (4 heads, 128 hidden dim)
    Each: Mean-Max Graph Pooling -> 256 dim
    |
    v
Multi-Window Fusion (768 -> 256 dim)
    |
    v
4-Layer Pre-LN Transformer Encoder
    + 5 Learnable Horizon Query Tokens
    -> 5 x 256-dim horizon representations
    |
    v
3 Prediction Heads per Horizon (15 heads total)
    Occurrence  -> sigmoid -> p_k in (0,1)
    Magnitude   -> linear  -> m_k in R
    Location    -> linear  -> (lat_k, lon_k) in R^2

Total parameters: 3,312,948
```

---

## Repository Structure

```
AfterShockNet/
|
├── README.md
├── LICENSE
├── requirements.txt
|
├── notebooks/
│   └── AfterShockNet_Full_Experiment.ipynb
|
├── data/
│   ├── README_data.md
│   └── sample_events.csv
|
└── weights/
    └── README_weights.md
```

---

## Installation

```bash
git clone https://github.com/[your-username]/AfterShockNet.git
cd AfterShockNet
pip install -r requirements.txt
```

Requires Python 3.9+, CUDA 11.8, and a GPU with at least 16 GB VRAM for training.

---

## Notebook

The complete experiment is contained in a single notebook:

```
notebooks/AfterShockNet_Full_Experiment.ipynb
```

It includes:

- Data preprocessing and ETAS feature computation
- Spatio-temporal graph construction (k-NN + ETAS edges)
- Model training with AdamW and OneCycleLR schedule
- Full evaluation across all five horizons
- Baseline comparisons (ETAS, XGBoost, LSTM, Transformer)
- Ablation study (4 variants)
- Wilcoxon signed-rank statistical significance tests
- All figure generation (21 figures)

Developed and executed on Kaggle with NVIDIA Tesla P100-PCIE 16 GB GPU.

---

## Data

The primary seismic catalog was compiled by the authors from the USGS FDSN Event Web Service and is not redistributed here. Researchers may reproduce an equivalent dataset from:

```
https://earthquake.usgs.gov/earthquakes/search/
Parameters: type = earthquake, M >= 2.5, 1900-2026
```

All geophysical context layers are freely available from their original providers. See [data/README_data.md](data/README_data.md) for full details.

A 100-event synthetic sample for pipeline testing is provided in `data/sample_events.csv`.

---

## Pre-trained Weights

Pre-trained model weights (best checkpoint, epoch 26) are available for download. See [weights/README_weights.md](weights/README_weights.md) for the download link and loading instructions.

---

## Hyperparameters

| Parameter                | Value              |
|--------------------------|--------------------|
| GNN hidden dimension     | 128                |
| GATv2 attention heads    | 4                  |
| GATv2 layers             | 3                  |
| Lookback windows         | 24h, 48h, 72h      |
| Max nodes per graph      | 80                 |
| k-NN neighbours          | 6                  |
| Spatial radius (edges)   | 300 km             |
| ETAS trigger threshold   | 0.05               |
| Transformer layers       | 4                  |
| Transformer heads        | 8                  |
| Dropout rate             | 0.1                |
| Learning rate (max)      | 1e-4               |
| Optimiser                | AdamW              |
| LR schedule              | OneCycleLR (15% warmup) |
| Max epochs               | 50                 |
| Early stopping patience  | 15                 |
| Best checkpoint epoch    | 26                 |
| Label spatial radius     | 150 km             |

---

## Citation

```bibtex
@article{albassam2026aftershocknet,
  author  = {Al Bassam, Abdullah and Malek, Md. Abdul and
             Shabbir, Lotifur and {Al Atick}, Anonno Md. Abdulla
             and Mahmud, Sultan and Aziz, Osman and
             Ahmed, Shamim and Owasi, Mukshit Safi},
  title   = {Hybrid Operational Aftershock Forecasting with
             Spatio-Temporal Graph Neural Networks, Transformers,
             and {ETAS} Using Global Seismic Catalogs and
             Geophysical Constraints},
  journal = {Computers & Geosciences},
  year    = {2026},
  note    = {Under Review}
}
```

---

## Authors

| Name                          | Affiliation                                          |
|-------------------------------|------------------------------------------------------|
| Abdullah Al Bassam            | Varendra University, Bangladesh                      |
| Md. Abdul Malek               | Varendra University, Bangladesh                      |
| Lotifur Shabbir               | BRAC University, Bangladesh                          |
| Anonno Md. Abdulla Al Atick   | Harbin University of Science and Technology, China   |
| Sultan Mahmud                 | Varendra University, Bangladesh                      |
| Osman Aziz                    | Varendra University, Bangladesh                      |
| Shamim Ahmed                  | Varendra University, Bangladesh                      |
| Mukshit Safi Owasi            | AIUB, Bangladesh                                     |

**Corresponding author:** Abdullah Al Bassam · abassamrajbd65@gmail.com

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.