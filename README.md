# Cough-Based Lung Cancer Detection: A Data-Centric Investigation of Harmonisation, Localisation, and Cross-Source Generalisation

This repository accompanies my MSc Artificial Intelligence dissertation:



The project investigates whether cough-derived spectrograms can support lung cancer detection under realistic public-data conditions. The main focus is not only model performance, but whether the results remain trustworthy after controlling for dataset leakage, source-specific artefacts, class imbalance, and cross-source generalisation.

---

## Project Overview

Public cough datasets offer a promising low-cost route for respiratory screening research. However, they are often heterogeneous because recordings may come from different devices, environments, preprocessing pipelines, colour maps, image sizes, and spectrogram generation settings. Under these conditions, machine learning models can achieve high scores by learning dataset-specific “fingerprints” rather than genuine disease-related cough patterns.

This project therefore follows a **data-centric and bias-aware approach**. The pipeline includes metadata rebuilding, spectrogram harmonisation, duplicate auditing, leakage-resistant splitting, supervised modelling, parameter-efficient LoRA fine-tuning, and leave-one-source-out anomaly detection.

The working dataset contained approximately **3,700 spectrogram images**, including around **3,670 healthy samples** and **30 lung-cancer samples**. The cancer-labelled examples came from one source, which made cross-source evaluation especially important.

---

## Main Figures

<img width="932" height="376" alt="image" src="https://github.com/user-attachments/assets/95dd1c8a-7b58-48f8-a62a-77a16004597e" />


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

##  Harmonisation Figure

<img width="955" height="483" alt="image" src="https://github.com/user-attachments/assets/92dbf4f6-bb1c-4f11-960f-10a567ff77c4" />


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

## Result Figures
**Kaggle-only LoRA summary**

<img width="702" height="462" alt="image" src="https://github.com/user-attachments/assets/87234eec-ce2b-4b8f-8c56-d01baeb785ed" />
<img width="605" height="419" alt="image" src="https://github.com/user-attachments/assets/d27df24b-ff1e-4e51-9883-55f9820ba146" />


 **LOSO performance**

<img width="677" height="509" alt="image" src="https://github.com/user-attachments/assets/95125cac-8213-433a-946a-02a75fa661d2" />
<img width="813" height="514" alt="image" src="https://github.com/user-attachments/assets/0f02b435-e665-4460-8614-1f5d67f4a1a8" />

**Aggregation × inversion audit**
<img width="875" height="429" alt="image" src="https://github.com/user-attachments/assets/3c30b506-e593-4862-909e-0fce98a6b5ac" />


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

- **Coswara**: respiratory sounds and symptom data, including cough recordings.  
  Link: https://github.com/iiscleap/Coswara-Data

- **COUGHVID**: large-scale crowdsourced cough audio dataset.  
  Link: https://www.epfl.ch/labs/esl/index-html/datasets/coughviddataset/

- **Kaggle CSI / Chest Diseases by Medical Imaging and Cough Sounds**: source of lung-cancer and normal cough spectral images used in the project.  
  Link: https://www.kaggle.com/datasets/sujaykapadnis/chest-diseases-by-medical-imaging-and-cough-sounds

Please check and follow the licence, citation, and access requirements of each dataset provider before reuse.

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

## Contributions

This dissertation makes the following contributions:

- built a canonical metadata index for heterogeneous cough spectrogram data;
- identified and documented shortcut-learning risk caused by source-specific visual fingerprints;
- implemented a harmonisation and de-duplication workflow to reduce leakage and visual confounding;
- compared within-source supervised learning with cross-source LOSO evaluation;
- evaluated ResNet, EfficientNet, ViT-LoRA, ensemble outputs, and healthy-only anomaly detection;
- demonstrated that LoRA can improve within-source discrimination under small-data conditions;
- implemented a cough-localisation and Mahalanobis-distance anomaly pipeline for cross-source testing;
- reported negative results, including weak multiclass performance and CORAL alignment collapse, to avoid overclaiming;
- packaged metadata, split files, prediction tables, manifests, thresholds, and notebooks for reproducibility.

---

## Main Conclusion

The main conclusion is that the binding constraint is **data quality and source generalisation**, not model architecture. Within a single source, ViT-LoRA can achieve useful discrimination, but cross-source evaluation shows that public cough datasets remain too fragile for strong clinical claims.

My main contribution was to move the project away from misleading high accuracy produced by source artefacts and toward a more rigorous, auditable evaluation framework. The final repository provides a reproducible package for harmonisation, metadata auditing, within-source modelling, LOSO anomaly detection, and transparent reporting of both positive and negative results.

This work should therefore be interpreted as a data-centric feasibility study and reproducibility package for future cough-based screening research, rather than as a clinically deployable lung cancer detector.

---

## Author

**Saud Abdullah**  
MSc Artificial Intelligence  
University of Lincoln
