# Multimodal Skin Lesion Classification Using Clinical Images and Patient Metadata

**COMP1013 — Multimodal Data Analysis**  
**Project 2.2: Individual Implementation**  
**University of Adelaide | Team 10**

## 1. Project Overview

This project investigates whether combining clinical skin lesion photographs with patient metadata improves six-class skin lesion classification compared with using either modality alone.

Five modelling approaches were implemented and evaluated using the PAD-UFES-20 dataset. The study focuses on classification performance, class imbalance, data leakage prevention, model reliability and ethical limitations.

**Research Question:** Does combining clinical images and patient metadata improve skin lesion classification compared with image-only and metadata-only models?

## 2. Dataset

The PAD-UFES-20 dataset contains 2,298 clinical photographs from 1,373 patients across six diagnostic categories:

- ACK — Actinic Keratosis
- BCC — Basal Cell Carcinoma
- MEL — Melanoma
- NEV — Melanocytic Nevus
- SCC — Squamous Cell Carcinoma
- SEK — Seborrheic Keratosis

### Patient-Disjoint Data Split

| Partition | Images | Patients |
|---|---:|---:|
| Training | 1,640 | 980 |
| Validation | 329 | 196 |
| Test | 329 | 197 |

Patients were kept separate across all three partitions to reduce data leakage.

The dataset is imbalanced, with only 37 melanoma images compared with 603 basal cell carcinoma images in the training set.

**Dataset source:** https://data.mendeley.com/datasets/zr7vgbcyr2/1

**Dataset publication:** https://doi.org/10.1016/j.dib.2020.106221

## 3. Data Preprocessing

### Image Processing

Clinical photographs were converted to RGB, resized to 224 × 224 pixels and normalised using ImageNet statistics.

A pretrained, frozen EfficientNet-B0 model extracted 1,280-dimensional image feature representations.

### Metadata Processing

Six clinical attributes were used: age, lesion region, itching, pain, bleeding and elevation.

Numerical features were standardised, and categorical variables were one-hot encoded, producing 27 features.

Preprocessing transformations were fitted using training data only.

## 4. Models Implemented

Five main modelling approaches were compared:

1. **Metadata-only:** Logistic regression using processed patient metadata.
2. **Image-only:** Logistic regression using EfficientNet-B0 image embeddings.
3. **Early fusion:** Combines image and metadata features before classification.
4. **Late fusion:** Combines predictions from separately trained image and metadata models.
5. **Intermediate gated fusion:** Uses metadata-conditioned gates to adjust image features.

The original proposal was adapted to accommodate computational limitations. EfficientNet-B0 was frozen instead of fine-tuned, training-time image augmentation was omitted, and a simplified MetaBlock-inspired gated network was implemented.

## 5. Model Evaluation

Macro-F1 was selected as the primary metric because the dataset contains substantial class imbalance.

Additional evaluation included accuracy, balanced accuracy, class-specific recall, confusion matrices, patient-grouped cross-validation, patient-bootstrap confidence intervals and robustness experiments.

### Held-Out Test Results

| Model | Accuracy | Macro-F1 |
|---|---:|---:|
| Original metadata | 0.5046 | 0.4215 |
| Tuned metadata | 0.5015 | 0.4244 |
| Tuned image-only | 0.6170 | 0.5298 |
| Early fusion | 0.6474 | **0.5542** |
| Late fusion | **0.6565** | 0.5273 |
| Intermediate gated fusion | 0.6170 | 0.5413 |

Late fusion was selected as the primary model before testing because it achieved the highest validation macro-F1 of 0.5794.

Early fusion achieved the highest observed test macro-F1 of 0.5542.

Compared with tuned image-only classification, the exploratory early-fusion improvement was 0.0245, with a paired patient-bootstrap 95% confidence interval of [0.0044, 0.0501]. This comparison was not adjusted for multiple model comparisons.

## 6. Limitations and Ethical Considerations

Important limitations include severe class imbalance, overfitting, systematic metadata missingness and potential dataset-specific shortcuts.

The selected late-fusion model correctly identified only one of seven melanoma test images.

The models have not been externally validated and should not be used for clinical diagnosis, screening or treatment decisions.

**This project is an educational research prototype, not a clinically validated diagnostic system.**

## 7. Repository Contents

- `Project2.2_Multimodal_Skin_Lesion_Classification_a1985234.ipynb` — Complete implementation notebook with saved experiments and evaluation results.
- `README.md` — Project documentation.
- `requirements_project2_2.txt` — Python dependencies.
- `.gitignore` — Git exclusion rules.

## 8. Software Requirements

The implementation was developed using:

- Python 3.9.13
- NumPy 1.26.4
- Pandas 2.3.3
- Scikit-learn 1.4.2
- PyTorch 2.8.0
- Torchvision 0.23.0
- Matplotlib 3.9.4

Install the dependencies using:

`python -m pip install -r requirements_project2_2.txt`

The original experiments were run on a Windows computer using CPU-based processing.

## 9. Reproducing the Experiments

1. Download PAD-UFES-20 from the original dataset provider.
2. Place the extracted clinical photographs inside `images/extracted/`.
3. Place the original patient-split CSV in the project directory.
4. Install the required Python packages.
5. Open the implementation notebook in Jupyter.
6. Run the notebook cells sequentially.

The original patient-split CSV is required for exact reproduction and is not included in this public repository.

**Original split SHA-256:**

`b6f17fa5efecc2507e4df7f8a0cee14dda97824c4425a0dcc0c2948173d99c32`

The pretrained EfficientNet-B0 weights must also be available or downloaded when running the notebook.

Dataset availability does not guarantee exact reproduction without the original split and compatible software environment.

## 10. References

Pacheco, A. G. C. et al. (2020). *PAD-UFES-20: A skin lesion dataset composed of patient data and clinical images collected from smartphones.* Data in Brief.  
https://doi.org/10.1016/j.dib.2020.106221

Pacheco, A. G. C. and Krohling, R. A. (2021). *An attention-based mechanism to combine images and metadata in deep learning models applied to skin cancer classification.* IEEE Journal of Biomedical and Health Informatics.  
https://doi.org/10.1109/JBHI.2021.3062002

Tan, M. and Le, Q. V. (2019). *EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks.*  
https://arxiv.org/abs/1905.11946
