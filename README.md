# PathoVision

**Whole-Slide Histopathology Cancer Detection, Segmentation and Quantification**

PathoVision is an end-to-end computational pathology pipeline for analysing gigapixel whole-slide histopathology images (WSIs) from the CAMELYON16 dataset.

It combines multi-resolution WSI processing, tumour patch classification, tumour segmentation, whole-slide inference, lesion detection, and tumour-burden quantification in a single reproducible workflow.

---

## Highlights

- Multi-resolution WSI reading with **OpenSlide**
- Tissue-region detection to avoid unnecessary background processing
- CAMELYON16 XML annotation parsing and rasterisation
- Leakage-safe **slide-level train / validation / test splitting**
- Histopathology stain normalisation
- **EfficientNet-B0** tumour patch classifier
- Hard-negative mining to reduce false positives
- Test-time augmentation (TTA)
- **U-Net with ResNet34 encoder** for tumour segmentation
- Overlapping whole-slide inference with probability blending
- Lesion detection and tumour-burden quantification
- Physical area estimation using microns-per-pixel (MPP) metadata
- Grad-CAM model inspection
- Kaggle GPU-compatible training pipeline

---

## Results

### Patch Classification

An ImageNet-pretrained EfficientNet-B0 classifier was fine-tuned using stain normalisation, slide-balanced sampling, hard-negative mining and 4-view test-time augmentation.

| Metric | Result |
|---|---:|
| Accuracy | **85.32%** |
| Balanced Accuracy | **83.21%** |
| AUROC | **0.906** |
| Precision | **95.24%** |
| Sensitivity / Recall | **68.97%** |
| Specificity | **97.44%** |
| F1 Score | **0.800** |
| Matthews Correlation Coefficient | **0.711** |

The decision threshold was selected using the validation set and then frozen for test evaluation.

---

### Tumour Segmentation

A U-Net with an ImageNet-pretrained ResNet34 encoder was trained for patch-level tumour segmentation.

| Metric | Result |
|---|---:|
| Dice Score | **0.765** |
| IoU | **0.620** |
| Pixel Precision | **0.890** |
| Pixel Recall | **0.672** |

The segmentation threshold was selected on the validation set.

---

### Whole-Slide Demonstration

On held-out slide `tumor_003`, the complete classifier-gated whole-slide pipeline achieved:

| Metric | Result |
|---|---:|
| Whole-slide Dice | **0.843** |
| Whole-slide IoU | **0.728** |
| Pixel Precision | **0.911** |
| Pixel Recall | **0.784** |
| Predicted Tumour Burden | **0.452%** |
| Ground-Truth Tumour Burden | **0.525%** |
| Absolute Burden Error | **0.073 percentage points** |

> **Note:** The whole-slide Dice / IoU above are from a single held-out WSI demonstration and should not be interpreted as aggregate CAMELYON16 benchmark performance.

---

## Pipeline

```text
Whole-Slide Image
        |
        v
Multi-resolution OpenSlide reader
        |
        v
Low-resolution tissue detection
        |
        v
Leakage-safe patch extraction
        |
        v
Stain normalisation
        |
        v
EfficientNet-B0 classifier
        |
        v
Suspicious-region selection
        |
        v
ResNet34 U-Net segmentation
        |
        v
Overlapping probability-map reconstruction
        |
        v
Morphological post-processing
        |
        v
Lesion detection + tumour quantification
