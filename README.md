# R2I-Medical-Imaging
# MeanSparse X-ray TB Classifier — Robustness Evaluation

Short README for the notebook that fine-tunes a **MeanSparse** model on the **tuberculosis-tb-chest-xray-dataset** and evaluates robustness with **PGD** attacks.
Code and experiments on bone fracture analysis will be added in a future update.

## Overview
- **Goal:** Detect Tuberculosis (TB) from chest X-rays and assess adversarial robustness.  
- **Model:** MeanSparse variant of **WideResNet-94-16** (backbone from the MeanSparse repo) with a 2-class head.  
- **Task:** Binary classification — *Normal* vs *Tuberculosis*.  
- **Notebook:** `xai-for-medical-imaging.ipynb`.

## Data
- **Dataset:** `tuberculosis-tb-chest-xray-dataset` (*TB_Chest_Radiography_Database*).  
- **Split strategy:** train/val/test with class balancing notes included in the notebook.  

## Training
- Fine-tune **only the classifier head** on top of a **frozen backbone**.  
- **Optimizer:** Adam &nbsp;&nbsp;|&nbsp;&nbsp; **LR:** `1e-3` &nbsp;&nbsp;|&nbsp;&nbsp; **Epochs:** `30`.  
- **Loss:** Cross-Entropy with **inverse-frequency class weights** to mitigate class imbalance.  
- Training/validation loops and checkpoints are implemented in-notebook.

## Robustness (PGD) Evaluation
- **Attack:** ℓ∞ **PGD** with **ε = 4/255**, **steps = 10**, **α = 1/255**, random start.  
- Evaluates **clean vs adversarial** accuracy and prints a **classification report** & **confusion matrix** on attacked samples.

