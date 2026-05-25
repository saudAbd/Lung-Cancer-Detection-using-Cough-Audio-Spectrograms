# Cough-Based Lung Cancer Detection from Cough Spectrograms

This repository accompanies my MSc Artificial Intelligence dissertation:

**Cough-Based Lung Cancer Detection: A Data-Centric Investigation of Harmonisation, Localisation, and Cross-Source Generalisation**

The project investigates whether cough-derived spectrograms can support lung cancer detection under realistic public-data conditions. The main focus is not only model performance, but whether the results remain trustworthy after controlling for dataset leakage, source-specific artefacts, class imbalance, and cross-source generalisation.

---

## Project Overview

Public cough datasets offer a promising low-cost route for respiratory screening research. However, they are often heterogeneous because recordings may come from different devices, environments, preprocessing pipelines, colour maps, image sizes, and spectrogram generation settings. Under these conditions, machine learning models can achieve high scores by learning dataset-specific “fingerprints” rather than genuine disease-related cough patterns.

This project therefore follows a **data-centric and bias-aware approach**. The pipeline includes metadata rebuilding, spectrogram harmonisation, duplicate auditing, leakage-resistant splitting, supervised modelling, parameter-efficient LoRA fine-tuning, and leave-one-source-out anomaly detection.

The working dataset contained approximately **3,700 spectrogram images**, including around **3,670 healthy samples** and **30 lung-cancer samples**. The cancer-labelled examples came from one source, which made cross-source evaluation especially important.

---

## Suggested Main Figure

Add **Figure 4** from the dissertation here:

![System Overview](docs/images/system_overview.png)

Figure 4 shows the overall system design. Harmonisation and de-duplication feed two analysis branches: a supervised modelling branch and a healthy-only anomaly detection branch.

---

## Methodology

The project has four main methodological components:

1. **Data harmonisation**  
   Spectrograms were standardised to reduce superficial source-specific differences. This included grayscale conversion, fixed image size, padding, and intensity normalisation.

2. **Duplicate and near-duplicate auditing**  
   Perceptual hashing was used to identify exact and near-duplicate spectrograms, reducing the risk of leakage across training and test splits.

3. **Within-source supervised classification**  
   ResNet, EfficientNet, and ViT-LoRA models were evaluated in a Kaggle-only setting to estimate the upper-bound performance when source shift is reduced.

4. **Cross-source LOSO anomaly detection**  
   A healthy-only anomaly detection route was implemented using cough localisation, frozen ResNet-18 embeddings, Ledoit-Wolf covariance shrinkage, and Mahalanobis distance.

---

## Suggested Harmonisation Figure

Add **Figure 2** from the dissertation here:

![Before and After Harmonisation](docs/images/harmonisation_example.png)

This figure is important because it visually shows the central data problem: models can exploit visual differences between dataset sources unless spectrograms are harmonised.

---

## Key Results

| Experiment | ROC-AUC | PR-AUC | Main Interpretation |
|---|---:|---:|---|
| Kaggle-only ViT-LoRA | 0.867 | 0.877 | Strong within-source performance under consistent source conditions |
| LOSO anomaly detection | 0.756 | 0.657 | Non-trivial cross-source signal, but with false-positive burden |
| CORAL alignment | 0.000 | 0.361 | Negative result; shallow alignment harmed anomaly separation |

The Kaggle-only LoRA experiment achieved **ROC-AUC = 0.867**, **PR-AUC = 0.877**, and **F1@τ\* = 0.684**. In the LOSO anomaly setting, the best mean-inverted aggregation configuration achieved **ROC-AUC = 0.756** and **PR-AUC = 0.657**. These findings suggest that useful signal may exist, but current public datasets are not sufficient for a clinically generalisable cough-based lung cancer detector.

---

## Suggested Result Figures

Add **Figure 12** and **Figure 11** from the dissertation here:

![Kaggle LoRA Summary](docs/images/kaggle_lora_summary.png)

![Kaggle LoRA Confusion Matrix](docs/images/kaggle_lora_confusion_matrix.png)

Add **Figure 14** or **Figure 15** for LOSO performance:

![LOSO ROC Curve](docs/images/loso_roc_curve.png)

![LOSO Mean-Inverted Summary](docs/images/loso_mean_inverted_summary.png)

Optional additional technical figure:

![Aggregation Inversion Audit](docs/images/aggregation_inversion_audit.png)

Recommended figures to add:

- **Figure 4**: System overview
- **Figure 2**: Before-after harmonisation
- **Figure 12**: Kaggle-only LoRA summary
- **Figure 11**: Kaggle-only LoRA confusion matrix
- **Figure 14 or Figure 15**: LOSO ROC / mean-inverted summary
- **Figure 17**: Aggregation × inversion audit

---

## Repository Structure

```text
Cough-Based-Lung-Cancer-Detection/
│
├── dataset/
│   └── metadata/
│       └── splits/
│
├── preprocessing_harmonisation/
│   ├── 01_data_harmonisation.ipynb
│   ├── 02_data_validation.ipynb
│   └── 03_regeneration.ipynb
│
├── supervised_models/
│   ├── results/
│   └── model notebooks
│
├── lora_vit_model/
│   └── results/
│
├── anomaly_loso/
│   └── LOSO anomaly notebooks
│
├── ensemble/
│   └── results/
│
├── final_package/
│   ├── configs/
│   ├── predictions/
│   ├── thresholds/
│   └── manifests/
│
├── docs/
│   └── images/
│
├── README.md
├── requirements.txt
└── LICENSE
```

---

## How to Run

Install the main dependencies:

```bash
pip install -r requirements.txt
```

Suggested workflow:

1. Run the preprocessing and harmonisation notebooks.
2. Review metadata and split files under `dataset/metadata/`.
3. Run the supervised model notebooks.
4. Run the LoRA/ViT experiments.
5. Run the LOSO anomaly detection notebooks.
6. Inspect predictions, manifests, thresholds, and metrics under `final_package/`.

---

## Data Availability

Raw audio files, generated spectrogram image folders, model checkpoints, and large intermediate files are not included in this repository due to size and licensing constraints.

This repository includes lightweight research artefacts only:

- metadata files
- split files
- notebooks
- predictions
- manifests
- threshold logs
- result tables

Users should download the original public datasets from their official sources before reproducing the full pipeline.

Datasets used in the project:

- Coswara
- COUGHVID
- Kaggle CSI / Chest Diseases Cough Sounds

---

## Limitations

This repository is a research artefact, not a clinical deployment system. The key limitations are:

- the lung-cancer class is very small;
- all cancer examples originate from one dataset source;
- source-label coupling remains a major threat;
- LOSO performance is sensitive to threshold choice;
- cough localisation is heuristic rather than manually annotated;
- larger multi-source cancer datasets are required before clinical claims can be made.

---

## Main Conclusion

The main conclusion is that the binding constraint is **data quality and source generalisation**, not model architecture. Within a single source, ViT-LoRA can achieve useful discrimination, but cross-source evaluation shows that public cough datasets remain too fragile for strong clinical claims.

The project contributes a reproducible harmonisation, evaluation, and anomaly-detection framework for future cough-based screening research.

---

## Citation

Abdullah, S. (2025). *Cough-Based Lung Cancer Detection: A Data-Centric Investigation of Harmonisation, Localisation, and Cross-Source Generalisation*. MSc Artificial Intelligence Dissertation, University of Lincoln.

---

## Author

**Saud Abdullah**  
MSc Artificial Intelligence  
University of Lincoln
