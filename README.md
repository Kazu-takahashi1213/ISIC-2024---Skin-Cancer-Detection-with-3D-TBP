![Joseph KZ - ISIC 2024 - Skin Cancer Detection with 3D-TBP](https://github.com/user-attachments/assets/aca05b7d-5998-4e4f-9236-e0301f97afd1)

<br>

Overview：
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

〇 Objective
To build a binary classification model that predicts whether a skin lesion is malignant or benign, using structured metadata and image-derived features.
The goal is to maximize performance on the ISIC 2024 leaderboard, primarily judged by ROC AUC.
<br>

〇 dataset Overview
Structured metadata (CSV):
Patient ID, age, lesion size/type, etc.

<br>
TBP features (3D Texture-Based Patterns):
Quantified indicators of color differences (ΔL, ΔB), contrast with surrounding skin, lesion area, shape (eccentricity), etc., extracted in the LAB color space.

<br>
External prediction scores (imagenet_predict):
Soft probabilities (0–1) from a pretrained ImageNet model for each sample.

<br>
〇 Pipeline Details
1. Feature Engineering
Selected 20+ numerical features from metadata and TBP

Included ΔL/ΔB, lesion contrast, area, eccentricity

Missing values filled (mean imputation or zero-fill)

Added Gaussian noise to ImageNet scores (e.g., ±0.0000003) to prevent overfitting (Magic Noise)

<br>
2. Model Construction (Ensemble)
Used VotingClassifier to ensemble:

LightGBM (weight: 0.5)

XGBoost (weight: 0.45)

CatBoost (weight: 0.1)

Soft voting strategy to balance strengths of each model

<br>
3. Cross-Validation & Sampling
Applied StratifiedGroupKFold (n=5) grouped by patient_id to prevent data leakage

Handled class imbalance via:

Over-sampling (e.g., SMOTE)

Under-sampling

<br>
4. Magic Noise Injection
Injected small Gaussian noise into external features (ImageNet scores) to:

Increase model diversity

Enhance generalization

Reduce overfitting

<br>
5. Evaluation & Final Prediction
Primary metric: ROC AUC

Averaged AUC across folds for validation

Final model retrained on full dataset

Predictions made using predict_proba() and exported to submission.csv

<br>
〇 Technical Highlights
Fusion of structured metadata and image-derived features

Balanced ensemble of three gradient boosting models

Noise-based regularization strategy (Magic Noise)

Robust validation via group-based stratification

<br>
