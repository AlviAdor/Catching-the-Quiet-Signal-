# Catching the Quiet Signal

Benchmarking signature-based detection (Suricata) against ML classifiers (Random Forest, k-NN, Gradient Boosting) for C2 (command-and-control) beacon detection, stratified by empirically derived beacon interval, on two public corpora: IoT-23 and CTU-13.

**Authors:** Md. Faizal Alvi Sarker, Md. Mazid-Ul-Haque (American International University — Bangladesh)

## Overview

This repository contains the full experimental pipeline behind the paper. The core contribution sits at the intersection of:

- Interval-stratified cross-corpus evaluation for C2 beaconing (three beacon-interval bands: B1/B2/B3)
- A label-purity finding for CTU-13's `From-Botnet` label — only ~2.2% of flows under that label (103 of 4,734 channels) correspond to genuine C&C-channel traffic
- SHAP-based mechanism analysis combined with interval stratification
- Bootstrap-confirmed reversal of in-corpus vs. cross-corpus detector rankings

## Notebooks

| Notebook | Description |
|---|---|
| `Catching The Quiet Signal Full Pipeline.ipynb` | Full 19-feature pipeline (including Suricata), with Random Forest, k-NN, and Gradient Boosting (`HistGradientBoostingClassifier`); includes SHAP explainability (per-band and cross-corpus). Run on Google Colab. |
| `Catching The Quiet Signal Feature Extraction.ipynb` | 9 timing-only features; corrected CTU-13 positive-class label semantics; bootstrap confidence intervals. Run locally (developed on macOS, M2 Pro). |

## Method summary

- **Evaluation:** Interval-stratified across three beacon-interval bands (B1/B2/B3), with GroupKFold on channel IDs to prevent leakage
- **Class imbalance:** In-fold SMOTE
- **Breakdown criteria:** Pre-registered τ=0.70 / δ=0.20, evaluated across multiple seeds
- **Positive-class definition:** Filtered via `C2_LABEL_TOKENS`; CTU-13's `From-Botnet` label verified to substantially over-count genuine C&C flows (see label-purity finding above)
- **Statistical validation:** Paired bootstrap CI (2,000 replicates), Benjamini-Hochberg FDR correction

## Key findings

- Gradient Boosting achieves the highest detection rate across all bands (peak DR = 0.976, F1 = 0.978 in B3), surpassing Random Forest as the in-corpus leader
- In-corpus detector rankings **reverse** under cross-corpus transfer: RF and GB collapse hardest, k-NN degrades least (confirmed via paired bootstrap CI, all pairwise differences significant)
- The relationship between beacon interval and detection difficulty is **not monotonic** — a U-shaped pattern, contrary to an earlier hypothesis
- The cross-corpus generalization failure holds even after correcting the CTU-13 positive-class definition, strengthening the finding

## Notes on scope

- Suricata's 0% detection result is intentionally retained; the reasoning (circular evaluation — Suricata is evaluated against traffic it was tuned to score) is documented in the pipeline code
- Reverse-direction cross-corpus transfer (CTU-13 → IoT-23) is deferred to future work due to insufficient positive samples (n_pos = 21 in IoT-23)

## Datasets

- [IoT-23](https://www.stratosphereips.org/datasets-iot23) (CSV)
- [CTU-13](https://www.stratosphereips.org/datasets-ctu13) (`.binetflow` / parquet)

## Requirements

- Python 3.x, scikit-learn, SHAP
- Suricata (signature-based baseline)
- Jupyter / Google Colab

## Citation

If you use this pipeline or findings, please cite the associated paper (details to be added on publication).
