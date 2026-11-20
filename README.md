# EXP-001: Brain Tumor MRI Classification Report

Public, standalone interactive HTML report for a research-only classification baseline on Cheng/Figshare dataset.

- Site: open index.html in a browser or use GitHub Pages.
- 5-fold patient-disjoint CV; 5 member ensemble/fold; 200 epochs/model.
- MRI examples use *reference* tumor masks, **not** predicted segmentation.
- All reported results are from an adapted PyTorch RIFA run. Not an exact reproduction of the original paper.
- No raw MRI dataset and no model checkpoints are published in this repo.

## Source
https://github.com/misti1203/Uncertainty-RIFA-Net
