# Interpretable & Robust AI for Medical Imaging — Bone Fracture Classification

This project focuses on **bone fracture classification** with an emphasis on **robustness** and **interpretability**. We fine-tune and evaluate multiple models on the **FracAtlas** dataset and assess adversarial robustness via **ℓ∞ PGD** attacks. We also document a related TB experiment for completeness.

## Overview
- **Primary task:** Bone fracture detection on radiographs (FracAtlas).
- **Models analyzed:**
  - [**MeanSparse**](https://github.com/SPIN-UMass/MeanSparse) (WRN-94-16 backbone with 2-class/ multi-class head)
  - [**Robust Principles**](https://github.com/poloclub/robust-principles) (robust baseline under evaluation)
- **Robustness:** Evaluate clean vs. adversarial performance using **PGD**.
- **Interpretability:** Designed to support XAI analyses (saliency/attribution; see notebook).

## Datasets
- **FracAtlas (current focus):** Train/val/test split with notes on class balance in the notebook.  
- **TB Chest X-ray (prior experiment):** `tuberculosis-tb-chest-xray-dataset` (*TB_Chest_Radiography_Database*).

## Training Setup
- **Approach:** Fine-tune (typically unfreeze head; optionally partial backbone) on FracAtlas for both **MeanSparse** and **Robust Principles**.
- **Typical defaults (adjust in notebook):**
  - Optimizer: **Adam**
  - LR: `1e-3` (with scheduler optional)
  - Epochs: `30`
  - Loss: **Cross-Entropy** with **class weights** if imbalance is present
- **Artifacts:** In-notebook training/validation loops, metrics logging, and checkpoint saving.

## Robustness Evaluation (PGD)
- **Attack:** ℓ∞ **PGD** with typical settings (configurable): `ε = 4/255`, `steps = 10`, `α = 1/255`, random start.  
- **Metrics:** Clean vs. adversarial accuracy, classification report, and confusion matrices on attacked samples.

### Robustness Results (FracAtlas)

| Model | Clean Accuracy (%) | PGD \(\ell_\infty\) Accuracy (%) |
|---|---:|---:|
| [MeanSparse](https://github.com/SPIN-UMass/MeanSparse) | XX.XX | XX.XX |
| [Robust_Principle](https://github.com/poloclub/robust-principles) | XX.XX | XX.XX |


## Planned Extensions
- **More robust models:** We plan to analyze additional robust architectures/training schemes (e.g., adversarially-trained baselines, TRADES-style, or other certified/empirical defenses).  
- **Status:** Code and experiments on additional robust models will be added in upcoming updates.

## Related TB Experiment (for reference)
- We previously fine-tuned **[MeanSparse](https://github.com/SPIN-UMass/MeanSparse)** on the **tuberculosis-tb-chest-xray-dataset** for **binary TB vs. Normal** classification and evaluated robustness using **ℓ∞ PGD**. Results and setup are summarized in the TB notebook (see `meansparse_model_tuberculosis.ipynb`).


