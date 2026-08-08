# Hybrid Intrusion Detection System Using Random Forest, ATEM and One-Class SVM

## Overview

This project presents a hybrid Intrusion Detection System (IDS) that combines
a supervised Random Forest classifier with an Adaptive Trust-based Ensemble
Mechanism (ATEM) and a One-Class Support Vector Machine (OCSVM).

The primary objective is to improve the detection of attack traffic that is
difficult for a conventional supervised classifier to identify, particularly
unknown or underrepresented attack types.

The proposed framework uses Random Forest as the primary classifier. ATEM
evaluates the reliability of the Random Forest prediction using confidence
and entropy-related information. Samples that are not sufficiently trusted
are routed to OCSVM for additional analysis.

### Proposed Framework

Random Forest → ATEM → OCSVM

The final system is:

**Random Forest + ATEM + OCSVM**

---

## Research Motivation

Traditional supervised intrusion detection models can achieve strong
performance on attack classes represented in their training data, but their
performance can decrease when previously unseen or poorly represented attack
traffic is encountered.

This project investigates whether an adaptive routing mechanism can improve
the detection of such difficult traffic by combining:

- Supervised classification
- Prediction confidence
- Prediction entropy
- One-class anomaly detection
- Adaptive routing

The main objective is to reduce false negatives while maintaining high
precision.

---

## Methodology

The overall workflow consists of the following stages:

1. Dataset preparation
2. Data cleaning and preprocessing
3. Feature preparation
4. Random Forest training
5. OCSVM training
6. Unknown-attack preparation
7. Random Forest prediction
8. Confidence and entropy calculation
9. ATEM training
10. ATEM threshold optimization
11. Adaptive routing
12. OCSVM analysis of routed samples
13. Hybrid IDS evaluation
14. False-negative analysis
15. Attack-wise evaluation

---

## Models

### 1. Random Forest

Random Forest is used as the primary supervised classifier.

It provides:

- Binary classification
- Class probabilities
- Prediction confidence

These outputs are used as inputs to the ATEM mechanism.

### 2. ATEM

The Adaptive Trust-based Ensemble Mechanism (ATEM) acts as a routing layer
between Random Forest and OCSVM.

ATEM uses Random Forest-related indicators, including:

- RF prediction
- RF confidence
- RF entropy
- OCSVM score

The selected ATEM threshold is:

**ATEM threshold = 0.50**

Samples considered sufficiently reliable are trusted by Random Forest.
Uncertain samples are routed to OCSVM.

### 3. One-Class SVM

OCSVM is used as the complementary anomaly detection component.

The selected OCSVM decision-score threshold is:

**OCSVM threshold = 40.7810**

---

## Dataset

The project uses the CICIDS2017 dataset.

The dataset contains benign traffic and multiple attack categories,
including:

- DoS Hulk
- DDoS
- PortScan
- DoS GoldenEye
- FTP-Patator
- DoS slowloris
- DoS Slowhttptest
- SSH-Patator
- Bot
- Web attacks
- Infiltration
- Heartbleed

Large raw and processed datasets are intentionally excluded from this
repository using `.gitignore`.

Users should obtain the dataset separately and place the required files in
the appropriate project directory.

---

## ATEM Dataset

The final ATEM dataset contained:

**25,122 samples**

with:

- 12,561 benign samples
- 12,561 attack samples

The ATEM dataset contained six fields:

- RF_Prediction
- RF_Confidence
- RF_Entropy
- OCSVM_Score
- Actual
- RF_Correct

The ATEM target was whether the Random Forest prediction was correct.

---

## ATEM Training

The ATEM dataset was divided into:

| Set | Samples |
|---|---:|
| Training | 17,585 |
| Testing | 7,537 |

Training target distribution:

| Target | Samples |
|---|---:|
| RF Correct | 10,080 |
| RF Incorrect | 7,505 |

The trained ATEM model achieved:

| Metric | Score |
|---|---:|
| Accuracy | 90.20% |
| Precision | 87.03% |
| Recall | 97.41% |
| F1-score | 91.93% |

---

## Final Hybrid IDS Results

The final held-out evaluation used:

**7,537 test samples**

The selected configuration was:

| Parameter | Value |
|---|---:|
| ATEM threshold | 0.50 |
| OCSVM threshold | 40.7810 |

### Final Performance

| Metric | Random Forest | RF + ATEM + OCSVM |
|---|---:|---:|
| Accuracy | 57.32% | **90.18%** |
| Precision | 99.83% | **96.55%** |
| Recall | 15.10% | **83.45%** |
| F1-score | 26.23% | **89.52%** |

### Final Confusion Matrix

| | Predicted Benign | Predicted Attack |
|---|---:|---:|
| Actual Benign | 3,636 | 113 |
| Actual Attack | 627 | 3,161 |

Therefore:

- True Negative = 3,636
- False Positive = 113
- False Negative = 627
- True Positive = 3,161

---

## False-Negative Reduction

One of the main objectives of the hybrid framework was to reduce false
negatives.

The Random Forest baseline produced:

**3,216 false negatives**

The final hybrid framework produced:

**627 false negatives**

Therefore:

**False-negative reduction = 2,589**

or approximately:

**80.50% reduction**

This demonstrates a substantial improvement in attack detection compared
with the Random Forest baseline on the held-out evaluation.

---

## Attack-wise Analysis

The hybrid framework produced substantial improvements for some attack
categories.

### DoS Slowhttptest

| Metric | Random Forest | Hybrid |
|---|---:|---:|
| Recall | 3.44% | **96.31%** |
| False Negatives | 1,544 | **59** |

False-negative reduction:

**96.18%**

### DoS slowloris

| Metric | Random Forest | Hybrid |
|---|---:|---:|
| Recall | 32.07% | **99.63%** |
| False Negatives | 1,095 | **6** |

False-negative reduction:

**99.45%**

### Bot

Bot remained a difficult attack category.

| Metric | Random Forest | Hybrid |
|---|---:|---:|
| Recall | 0.00% | 2.60% |
| False Negatives | 577 | 562 |

The analysis shows that Bot traffic remains an important limitation of the
current framework.

---

## Adaptive Routing

On the held-out evaluation set:

- **4,835 samples (64.15%)** were trusted by ATEM.
- **2,702 samples (35.85%)** were routed to OCSVM.

Among the Random Forest errors:

- RF errors: **3,217**
- Errors rejected by ATEM: **2,590**
- Errors recovered by OCSVM: **1,862**

The OCSVM recovery among rejected Random Forest errors was approximately:

**71.89%**

---

## Project Structure

```text
Hybrid_IDS_Project/
│
├── .gitignore
├── README.md
│
└── dataset/
    │
    ├── 01_Data_Understanding.ipynb
    ├── 02_Data_Preprocessing.ipynb
    ├── 03_Data_Cleaning.ipynb
    ├── 04_Data_Preparation.ipynb
    ├── 05_MLP_Model.ipynb
    ├── 06_CNN_Model.ipynb
    ├── 07_OCSVM_Model.ipynb
    ├── 09_Unknown_Attack_Test.ipynb
    ├── 10_Results_Visualization.ipynb
    ├── 11_ROC_PR_Curves.ipynb
    ├── 12_RF_Confidence_Analysis.ipynb
    ├── 13_Hybrid_Routing_Optimization.ipynb
    ├── 14_OCSVM_Complementarity_Analysis.ipynb
    ├── 15_ATEM_Training.ipynb
    │
    ├── figures/
    │
    └── results/
        ├── attackwise_results.csv
        ├── attackwise_recall.png
        ├── false_negative_comparison.png
        ├── final_configuration.json
        ├── final_model_comparison.csv
        └── final_model_performance.png
