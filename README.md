# BrainTumorDatasets

A public dataset repository for **brain tumor MRI segmentation and classification** experiments associated with the manuscript:

> **Segmentation-Prior-Guided Brain Tumor MRI Classification via SAM2-UNet and Four-Channel ResNet50**

This repository contains the MRI datasets used to train a tumor segmentation model and evaluate a segmentation-guided four-class brain tumor classifier. The cleaned classification dataset was prepared to reduce duplicate and near-duplicate leakage in the test set.

## Overview

The study uses two complementary 2D brain MRI datasets:

| Folder | Source / role | Classes | Annotation type | Main use |
|---|---|---|---|---|
| `dataset1/` | Figshare Brain Tumor Dataset | `glioma`, `meningioma`, `pituitary` | MRI image + pixel-level tumor mask | Binary tumor segmentation |
| `dataset2/` | Original Kaggle Brain Tumor MRI Dataset layout | `glioma`, `meningioma`, `notumor`, `pituitary` | Image-level labels | Reference/original classification dataset |
| `dataset2_clean/` | Deduplicated version of the Kaggle classification dataset | `glioma`, `meningioma`, `notumor`, `pituitary` | Image-level labels | Four-class classification benchmark |

## Repository structure

```text
BrainTumorDatasets/
├── dataset1/
│   ├── glioma/
│   │   ├── *_mri.jpg
│   │   └── *_mask.jpg
│   ├── meningioma/
│   │   ├── *_mri.jpg
│   │   └── *_mask.jpg
│   ├── pituitary/
│   │   ├── *_mri.jpg
│   │   └── *_mask.jpg
│   └── dataset1_cv.csv
│
├── dataset2/
│   ├── Training/
│   │   ├── glioma/
│   │   ├── meningioma/
│   │   ├── notumor/
│   │   └── pituitary/
│   └── Testing/
│       ├── glioma/
│       ├── meningioma/
│       ├── notumor/
│       └── pituitary/
│
└── dataset2_clean/
    ├── Training/
    │   ├── glioma/
    │   ├── meningioma/
    │   ├── notumor/
    │   └── pituitary/
    └── Testing/
        ├── glioma/
        ├── meningioma/
        ├── notumor/
        └── pituitary/
```

## Dataset details

### `dataset1`: segmentation dataset

`dataset1` is derived from the Figshare brain tumor dataset released by Cheng (2017). It contains contrast-enhanced T1-weighted brain MRI slices from three tumor categories:

| Class | Number of slices |
|---|---:|
| Glioma | 1,426 |
| Meningioma | 708 |
| Pituitary tumor | 930 |
| **Total** | **3,064** |

Each MRI slice has a corresponding manually annotated tumor mask. In this repository, images and masks are organized by class, and paired samples follow the naming pattern:

```text
<id>_mri.jpg
<id>_mask.jpg
```

This dataset does **not** include a `notumor` class and is intended for supervised binary tumor segmentation rather than four-class classification.

### `dataset2`: original classification dataset

`dataset2` follows the original Kaggle Brain Tumor MRI Dataset structure with separate `Training/` and `Testing/` directories. It contains four image-level classes:

```text
glioma
meningioma
notumor
pituitary
```

This folder is kept as a reference copy of the original classification dataset layout.

### `dataset2_clean`: cleaned classification benchmark

`dataset2_clean` is the cleaned version used for the classification experiments in the manuscript. The cleaned test set was produced by removing exact and near-duplicate images from the original Kaggle test split.

The cleaning procedure used:

1. **MD5 hashing** for exact duplicate detection.
2. **Perceptual hashing** to identify near-duplicate candidates.
3. **Structural Similarity Index Measure (SSIM)** to confirm near duplicates.
4. SSIM thresholds:
   - `0.95` for within-Kaggle comparisons.
   - `0.80` for cross-dataset comparisons with the Figshare dataset.

After duplicate removal, the cleaned test set contains **810 images**:

| Class | Test images |
|---|---:|
| Glioma | 250 |
| Meningioma | 174 |
| No tumor | 140 |
| Pituitary tumor | 246 |
| **Total** | **810** |

Because the original Kaggle dataset does not provide consistent patient identifiers, complete patient-level independence cannot be fully guaranteed. The cleaned split should therefore be interpreted as a **benchmark-level** evaluation set rather than a clinically validated external cohort.

## Intended experimental use

### Segmentation

Use `dataset1` for binary tumor segmentation:

```text
Input:  dataset1/<class>/<id>_mri.jpg
Target: dataset1/<class>/<id>_mask.jpg
```

In the associated study, SAM2-UNet was trained on `dataset1` to generate tumor-region masks.

### Classification

Use `dataset2_clean` for four-class MRI classification:

```text
Training: dataset2_clean/Training/<class>/*.jpg
Testing:  dataset2_clean/Testing/<class>/*.jpg
```

In the associated study, the predicted tumor mask was concatenated with the grayscale MRI image as a fourth channel and passed to a modified ResNet50 classifier.

The four classification labels are:

```text
0: glioma
1: meningioma
2: notumor
3: pituitary
```

## Download

Clone the repository:

```bash
git clone https://github.com/TOMMY13241231/BrainTumorDatasets.git
cd BrainTumorDatasets
```

## Data sources

Please cite and respect the original data sources:

1. Cheng, J. (2017). **Brain tumor dataset**. Figshare.  
   https://doi.org/10.6084/m9.figshare.1512427.v5

2. Nickparvar, M. **Brain Tumor MRI Dataset**. Kaggle.  
   https://www.kaggle.com/datasets/masoudnickparvar/brain-tumor-mri-dataset
