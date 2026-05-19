# README

## Project
**Comparing Connectivity-Aware Losses for Retinal Vessel Segmentation**

## Overview
This project studies how different loss functions affect connectivity preservation in retinal vessel segmentation under the same U-Net framework. The code trains and compares five loss settings:

- BCE + Dice
- Weighted BCE + Dice
- BCE + Dice + clDice
- BCE + Dice + Skeleton Recall
- BCE + Dice + cbDice-like loss

The experiments are conducted on a combined retinal vessel dataset built from:

- DRIVE
- STARE
- CHASE_DB1
- HRF

The code was written in Python Jupter Notebook
required packages to run:
PIL,
Numpy,
shutil
os
tensorflow
matplotlib
pandas

Preprocessing_with_ablation.ipynb:
- dataset pairing and preprocessing
- augmentation
- U-Net model building
- 5-fold cv training for BCE
- small overfitting test for BCE
- 5-fold cv for BCE + Dice
- Training precess for Dice + five different loss
- metric evaluation
- visualization of predictions and training curves


loss.ipybn
- Defined U-Net structure
-BCE+Dice 5-fold CV
-loss functions, plotting functions helpers,
-New train test split after shuffling
-Final training functions for comparison
-training pipeline for all 5 models
-test and evaluation
---

How to run
Run the section "Preprocessing" in Preprocessing_ablation.ipynb to generate cleaned datasets, save them into desired directory by editing DATA_ROOT, COMINED_IMG_DIR, COMBINED_MASK_DIR,RESULT_ROOT
Run sections from U-Net to Test Model-wise in "loss.ipynb" for training and testing results