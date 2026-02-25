# Segmentation-Guided Counterfactual MRI Analysis for Causal Interpretation of Brain Tumor Classifiers

## Overview

This repository presents a deep learning–based brain tumor classification pipeline combined with a segmentation-guided counterfactual analysis framework for interpretability.

While convolutional neural networks achieve high predictive accuracy on medical imaging tasks, their decision-making process is often opaque. This project introduces a deterministic, mask-guided counterfactual evaluation strategy to quantify how strongly a classifier depends on tumor regions for its predictions.

The framework combines:

- ResNet-50–based tumor classification  
- Patient-level data splitting to prevent leakage  
- Mask-guided counterfactual image generation  
- Quantitative causal consistency metrics  

The objective is not only high accuracy, but verifiable model trustworthiness.

---

## Dataset

- **Source:** Figshare Brain Tumor Dataset  
- **DOI:** 10.6084/m9.figshare.1512427.v8  
- **Modality:** T1-weighted contrast-enhanced MRI  
- **Classes:**
  - Meningioma
  - Glioma
  - Pituitary tumor  
- Includes ground-truth tumor segmentation masks  

All train, validation, and test splits are performed at the **patient level** to avoid data leakage.

---

## Methodology

### Preprocessing

- Intensity clipping (1st–99th percentile)  
- Min-max normalization followed by z-score standardization  
- Resizing to CNN input resolution  
- Mask resizing with nearest-neighbor interpolation  

---

### Baseline Classification Model

- **Backbone:** ResNet-50 (ImageNet pretrained)  
- **Architecture modifications:**
  - Global Average Pooling
  - Dense layer (256 units, ReLU)
  - 3-class softmax output  
- **Loss Function:** Categorical Focal Loss  
- **Optimizer:** Adam with learning rate scheduling  
- Early stopping applied  

### Test Performance

- **Overall Accuracy:** 94%

| Class       | Precision | Recall | F1-score |
|------------|-----------|--------|----------|
| Meningioma | 0.83      | 0.90   | 0.86     |
| Glioma     | 0.97      | 0.93   | 0.95     |
| Pituitary  | 0.98      | 0.98   | 0.98     |

Misclassifications primarily occur between meningioma and glioma due to visual similarity.

---

## Counterfactual Framework

To evaluate causal dependence on tumor regions, two counterfactual variants are generated for each test MRI.

### Tumor-Removed MRI

- Tumor region removed using Navier–Stokes inpainting  
- Tests whether confidence decreases when pathological region is removed  

### Tumor-Only MRI

- Background suppressed  
- Only tumor pixels retained  
- Tests whether tumor region alone is sufficient for prediction  

This approach is deterministic, mask-guided, and does not require generative modeling.

---

## Counterfactual Consistency Metrics

Let:

- `P_orig` = probability of ground-truth class on original MRI  
- `P_remove` = probability after tumor removal  
- `P_only` = probability on tumor-only image

###Delta-Drop
Delta-Drop = P_orig - P_remove

Measures reduction in confidence after tumor removal.  
Large positive values indicate tumor-dependent decision behavior.

### Delta-Focus
Delta-Focus = P_only

Measures model activation when only tumor is present.  
Higher values indicate tumor-localized reasoning.

---

## Key Findings

- Tumor removal produces consistent confidence drops across correctly classified samples.
- Tumor-only images retain substantial probability mass for the true class.
- Misclassified cases show lower Delta-Drop and Delta-Focus values.
- Pituitary tumors demonstrate the strongest causal consistency.

The results suggest that the classifier relies on clinically meaningful tumor features rather than spurious background patterns.

---


## Limitations

- Relies on ground-truth segmentation masks  
- Inpainting may introduce minor visual artifacts  
- Single-modality MRI only  
- Dataset size limited relative to clinical-scale datasets  

---


## Conclusion

This project presents a unified framework combining high-performance classification with quantitative counterfactual analysis. By explicitly intervening on tumor regions and measuring prediction shifts, the method provides causal evidence of model reasoning.

The approach offers a practical pathway toward more transparent and clinically trustworthy medical AI systems.

