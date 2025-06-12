![Joseph KZ - ISIC 2024 - Skin Cancer Detection with 3D-TBP](https://github.com/user-attachments/assets/aca05b7d-5998-4e4f-9236-e0301f97afd1)

<br>

Overview
This project presents a high-performance structured data classification pipeline developed for the ISIC 2024 Skin Cancer Classification Challenge, aiming to predict malignant vs. benign skin lesions.
<br>
Key techniques include:

3D Texture-Based Pattern (TBP) features

External prediction scores from ImageNet-based models

Ensemble model using LightGBM, XGBoost, and CatBoost with soft voting

Patient-level cross-validation with Stratified Group K-Fold

Noise injection ("Magic Noise") to enhance generalization

<br>
By combining these techniques, we developed a robust, reproducible, and generalizable model optimized for ROC AUC, the primary evaluation metric of the competition.
<br>

Objective
To build a binary classification model that predicts whether a skin lesion is malignant or benign, using structured metadata and image-derived features.
The goal is to maximize performance on the ISIC 2024 leaderboard, primarily judged by ROC AUC.
<br>

Dataset Overview
Structured metadata (CSV):
Patient ID, age, lesion size/type, etc.

<br>
TBP features (3D Texture-Based Patterns):
Quantified indicators of color differences (ΔL, ΔB), contrast with surrounding skin, lesion area, shape (eccentricity), etc., extracted in the LAB color space.

<br>
External prediction scores (imagenet_predict):
Soft probabilities (0–1) from a pretrained ImageNet model for each sample.

<br>
Pipeline Details
1. Feature Engineering
Selected 20+ numerical features from metadata and TBP

Included ΔL/ΔB, lesion contrast, area, eccentricity

Missing values handled using mean imputation or zero-fill

Injected Gaussian noise into ImageNet scores (e.g., ±0.0000003) to reduce overfitting (Magic Noise)

<br>
2. Model Construction (Ensemble)
Built a VotingClassifier ensemble using:

LightGBM (weight: 0.5)

XGBoost (weight: 0.45)

CatBoost (weight: 0.1)

Used soft voting to leverage the strengths of each model

<br>
3. Cross-Validation and Sampling
Employed StratifiedGroupKFold (n=5) with patient ID grouping to avoid data leakage

Applied a combination of over-sampling (e.g., SMOTE) and under-sampling for class balance

<br>
4. Noise Injection (Magic Noise)
Injected small Gaussian noise into external features (ImageNet scores) to:

Increase model diversity

Improve generalization

Suppress overfitting

<br>
5. Evaluation and Final Prediction
Evaluation metric: ROC AUC

Averaged AUC across folds during validation

Retrained final model on the full dataset

Generated predictions using predict_proba() and formatted output as submission.csv

<br>
Technical Highlights
Integration of structured metadata with image-derived features

Balanced ensemble of three gradient boosting frameworks

Use of noise-based regularization to improve robustness

Reliable group-aware validation strategy to ensure reproducibility

<br>
