# Comparing connectivity-aware losses for retinal vessel segmentation

## Overview
Compares five loss functions under the same U-Net architecture to study how loss design affects a segmentation model's ability to preserve thin, connected vessel structures — structures that standard pixel-overlap losses tend to fragment even when overall accuracy looks fine. Final project for BGSU CS7200 (Machine Learning).

## Data
109 retinal image/mask pairs combined from four public datasets: DRIVE (20), STARE (16), CHASE_DB1 (28), and HRF (45). All images resized to 512x512, normalized to [0,1], with light augmentation (horizontal/vertical flips, ~10.8° rotation, mild brightness/contrast jitter) to reduce overfitting on a relatively small, multi-source dataset.

## Approach
- **Architecture:** fixed U-Net (4 encoder blocks at 32/64/128/256 channels, 512-channel bottleneck, symmetric decoder with skip connections), kept identical across all experiments so the loss function was the only variable
- **Baseline selection:** validated BCE+Dice stability via 5-fold cross-validation before fixing a 73:12:15 train/val/test split for all subsequent comparisons
- **Five loss settings compared**, all built on a BCE+Dice baseline:
  - BCE+Dice (baseline)
  - Weighted BCE+Dice (addresses vessel/background class imbalance)
  - BCE+Dice+clDice (topology-aware centerline agreement term)
  - BCE+Dice+Skeleton Recall (encourages recovery of the ground-truth centerline)
  - BCE+Dice+cbDice-like (combined centerline + boundary term)
- **Training:** batch size 4, learning rate 1e-4, dropout 0.1, early stopping on validation soft Dice (patience 15, starting epoch 20), automatic LR reduction (factor 0.5, patience 5)
- **Evaluation:** standard segmentation metrics (Precision, Recall, Dice, IoU) plus connectivity-aware metrics (clDice, connected-component error, skeleton recall, Betti number error) and qualitative visualization across five representative test images
- **Ablation study:** tested BCE alone and Dice alone as standalone objectives, and connectivity-aware terms without the BCE+Dice baseline, to confirm the baseline's necessity

## Results
**BCE+Dice+clDice performed best overall** on the held-out test set — highest Precision (0.752), Dice (0.752), IoU (0.605), clDice (0.765), and Skeleton Recall (0.689), plus the lowest β1 and Betti errors. BCE+Dice+cbDice ranked second across nearly the same metrics. Weighted BCE+Dice achieved the highest Recall (0.766) but at a real cost to Precision (0.602) and Dice (0.670) — it recovers more vessel pixels but introduces more false positives, visible as scattered noise around vessel structures.

Visualization confirmed the numbers: BCE+Dice produces clean major vessel skeletons but loses thin sub-branches and vessel tips; clDice- and cbDice-based losses preserved these fine branches noticeably better.

The ablation study showed BCE alone or Dice alone, even trained to 100 epochs, failed to recover meaningful vessel structure (the model collapsed to predicting a large undifferentiated disk, plateauing around 0.35 validation soft Dice) — confirming that connectivity-aware terms work as refinements on top of a solid overlap-based baseline, not as standalone replacements for one.

## Limitations
The connectivity-aware losses (clDice, Skeleton Recall, cbDice) are practical approximations of their published forms, not full reproductions — e.g., the Skeleton Recall implementation omits the original paper's focal variant, and the cbDice-like loss omits its geometric/radius-aware mechanisms. Loss term weights (fixed at 0.1) were not extensively tuned. Preliminary cross-validation showed some split sensitivity, likely from combining four datasets with differing imaging styles.

## How to run
1. Download DRIVE, STARE, CHASE_DB1, and HRF datasets from their respective sources
2. Install dependencies: `pip install tensorflow keras numpy opencv-python matplotlib scikit-learn`
3. Run `Preprocessing_with_ablation.ipynb` to prepare data and reproduce the ablation study
4. Run `loss_compare.ipynb` to train and compare the five loss settings
5. See `Yang_CS7200_Project_Report.pdf` for full methodology, mathematical loss definitions, and complete results tables/figures

## Status
Completed as the final project for BGSU CS7200 (Machine Learning). Full written report included alongside the implementation notebooks.

## Tech stack
Python, TensorFlow, Keras, U-Net, NumPy, OpenCV, scikit-learn
