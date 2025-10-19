# Interpretable & Robust AI for Medical Imaging — Bone Fracture Classification

[![Paper](https://img.shields.io/badge/Paper-PDF-blue.svg)](https://drive.google.com/file/d/12FdN3W_TRrUtb1t0qB0qAq7nW5wP9mhD/view?usp=sharing)  

This repo contains the medical-imaging experiments from our paper: we fine-tune robust CNNs on FracAtlas to build fracture classifiers that are robust and interpretable, showing a smaller accuracy drop under ℓ∞-PGD and more localized attributions than standard models.

## What’s in this repo
- **Task:** Fracture detection on radiographs (**FracAtlas**).
- **Backbone transfer:** Robust CIFAR-10 checkpoints from RobustBench; **freeze the backbone** and fine-tune a **linear head**.
- **Threat model:** ℓ∞ **PGD** (ε=4/255, 10 steps, step size 1/255, random start).
- **Explanations:** Saliency, DeepLIFT, Integrated Gradients (via Captum).

## Models evaluated (medical imaging)
- *Data Filtering for Efficient Adversarial Training* (Chen et al., 2024) 📄 [paper](https://www.sciencedirect.com/science/article/pii/S0031320324001456) | Architecture: *WideResNet-34-20*
- *Geometry-aware Instance-reweighted Adversarial Training* (Zhang et al., 2020) 📄 [paper](https://arxiv.org/abs/2010.01736) | *WideResNet-28-10*
- *Exploring Architectural Ingredients of Adversarially Robust Deep Neural Networks* (Huang et al., 2021) 📄 [paper](https://arxiv.org/abs/2110.03825) | *WideResNet-34-R*
- *Helper-based Adversarial Training: Reducing Excessive Margin to Achieve a Better Accuracy–Robustness Trade-off* (Rade & Moosavi-Dezfooli, ICLR 2022) 📄 [paper](https://openreview.net/forum?id=Azh9QBQ4tR7)
| *WideResNet-34-10*
- *Fixing Data Augmentation to Improve Adversarial Robustness* (Rebuffi et al., 2021) 📄 [paper](https://arxiv.org/abs/2103.01946) | *WideResNet-70-16*
- *Standardly trained model* (Croce et al., 2020) 📄 [paper](https://arxiv.org/abs/2010.09670) | *WideResNet-28-10*
  
> **Architecture note:** We use the RobustBench implementations/checkpoints for each method.

## Dataset
- **FracAtlas**: fracture vs. non-fracture radiographs with expert bounding boxes for localization.  
  (We construct imbalanced-aware splits and use bounding boxes for attribution-coverage evaluation.)

## Training & Eval
- **Optimization:** Adam (lr=1e-3), batch size=8, up to 25 epochs with early stopping (patience=5).
- **Frozen features:** only the final linear layer is trained.
- **Robustness:** evaluate **clean** and **PGD** accuracy; we also report **ΔAcc = clean − PGD**.
- **Interpretability:** generate per-image attributions (Saliency / DeepLIFT / Integrated Gradients) and compute **coverage** of salient pixels inside fracture boxes.

## Results (FracAtlas, ℓ∞ PGD ε=4/255, 10 steps)

**Table 1. Robustness results of different RobustBench models under PGD attack for the medical imaging task (ϵ = 4/255).**
| Model | Performance Rank | RobustBench Rank | Test Acc (%) | PGD (4/255) (%) | Δ Acc (%) | Fine-tuned Checkpoint |
|---|---:|---:|---:|---:|---:|:--:|
| Chen et al., 2024  | 1 | 38 | 85.14 | 68.92 | 16.22 | [ckpt](https://huggingface.co/MohammadFazli/xAI-fracture-xray-model/blob/main/best_data_filtering_wrn_34_20.pth) |
| Zhang et al., 2020 | 2 | 35 | 82.43 | 56.31 | 26.12 | [ckpt](https://huggingface.co/MohammadFazli/xAI-fracture-xray-model/blob/main/best_geometry_aware.pth) |
| Huang et al., 2021 | 3 | 24 | 75.23 | 42.34 | 32.89 | [ckpt](https://huggingface.co/MohammadFazli/xAI-fracture-xray-model/blob/main/best_wideresnet_34.pth) |
| Rade & Moosavi-Dezfooli, ICLR 2022 | 4 | 21 | 96.40 | 59.46 | 36.94 | [ckpt](https://huggingface.co/MohammadFazli/xAI-fracture-xray-model/blob/main/best_helper_wrn_34_10.pth) |
| Rebuffi et al., 2021 | 5 | 11 | 95.05 | 53.15 | 41.90 | [ckpt](https://huggingface.co/HosseinRanjbar/XAI-Bone-Fracture-Xray-Robust-Principle-Model/blob/main/best_Rebuffi2021Fixing_106_16_cutmix_ddpm.pth) |
| Standard | 6 | 99 | 85.59 | 0.00 | 85.59 | [ckpt](https://huggingface.co/HosseinRanjbar/XAI-Bone-Fracture-Xray-Robust-Principle-Model/blob/main/standard.pth) |

**Table I.** Attribution Coverage (%) at selected percentiles (5, 25, 50) for six RobustBench models using Saliency Maps (SM), DeepLIFT (DL), and Integrated Gradients (IG).

| Model | SM-5 | SM-25 | SM-50 | DL-5 | DL-25 | DL-50 | IG-5 | IG-25 | IG-50 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Chen et al. [11]    | 24.08 | 68.32 | 86.08 | 10.04 | 36.24 | 53.11 | 7.85 | 23.14 | 37.06 |
| Zhang et al. [25]   | 16.68 | 64.43 | 87.00 | 8.09  | 30.82 | 52.36 | 5.11 | 18.83 | 30.37 |
| Huang et al. [10]   | 20.75 | 63.59 | 83.39 | 9.24  | 23.95 | 35.60 | 5.76 | 15.31 | 26.62 |
| Rade et al. [9]     | 13.74 | 39.78 | 59.40 | 2.61  | 12.58 | 26.69 | 3.80 | 16.47 | 28.91 |
| Rebuffi et al. [7]  | 10.46 | 35.26 | 55.33 | 1.93  | 10.09 | 22.04 | 2.87 | 11.71 | 22.23 |
| Standard [31]       | 0.44  | 7.97  | 27.28 | 0.19  | 3.78  | 13.35 | 0.46 | 7.17  | 24.62 |

*Figure:* FracAtlas example with the fracture ROI (red box). The robust model produces tight, fracture-aligned responses across feature maps, while the standard model shows diffuse, background-biased activations.

<img width="2659" height="2462" alt="interpretability_maps" src="https://github.com/user-attachments/assets/901316bd-7206-4c6d-9152-84d373d65775" />


> **Interpretability summary:** Robust models exhibit **higher attribution coverage** within fracture regions and maintain focus under attack; the non-robust baseline drifts to background cues.


## Related TB Experiment (for reference)
- We previously fine-tuned **[MeanSparse](https://github.com/SPIN-UMass/MeanSparse)** on the **tuberculosis-tb-chest-xray-dataset** for **binary TB vs. Normal** classification and evaluated robustness using **ℓ∞ PGD**. Results and setup are summarized in the TB notebook (see `meansparse_model_tuberculosis.ipynb`).


