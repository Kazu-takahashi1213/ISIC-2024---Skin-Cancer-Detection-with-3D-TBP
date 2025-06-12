![Joseph KZ - ISIC 2024 - Skin Cancer Detection with 3D-TBP](https://github.com/user-attachments/assets/aca05b7d-5998-4e4f-9236-e0301f97afd1)

<br>

A high-performance, ensemble-based classification pipeline that predicts whether a skin lesion is malignant or benign—leveraging structured data, texture features, and external model scores.
<br>

Designed for the ISIC 2024 Challenge, this model achieved a silver medal by combining tabular data modeling techniques with carefully engineered features and robust validation strategies.

<br>
Overview
This project focuses on structured data classification for skin cancer diagnosis, using non-image features such as patient metadata, engineered texture descriptors, and scores from external models.
<br>
Key techniques:

Ensemble of LightGBM, XGBoost, and CatBoost

Integration of TBP (Texture-Based Pattern) features

External scores from ImageNet models

VotingClassifier with weighted soft voting

StratifiedGroupKFold validation (by patient)

"Magic Noise" injection for generalization

<br>
Task Objective
To build a binary classifier that predicts whether a skin lesion is malignant or benign, using structured tabular features derived from patient data and image-based descriptors.
<br>
The model is optimized for ROC AUC, the main metric used in the ISIC 2024 competition.
<br>

Feature Inputs
The final model uses a mix of metadata, engineered texture features, and external prediction scores:
<br>

Feature Type	Description
Metadata (CSV)	Patient ID, age, lesion size, lesion type, etc.
TBP Features	LAB color-space descriptors (ΔL, ΔB), area, eccentricity, skin contrast
ImageNet Scores	Pretrained CNN output probabilities (float values between 0–1)

<br>
Preprocessing & Feature Engineering
Selected 20+ numeric features (e.g., ΔL, ΔB, lesion size, area, shape)

Handled missing values using mean imputation or zero-fill

Injected small Gaussian noise into ImageNet-based features

Example: ±0.0000003

Purpose: promote output diversity and suppress overfitting

<br>
Model Architecture
Ensemble Model
The core classifier is a VotingClassifier that combines three gradient boosting models:
<br>

Model	Weight
LightGBM	0.50
XGBoost	0.45
CatBoost	0.10

<br> Soft voting is used to aggregate probability outputs across models. <br>
Training & Validation
Cross-Validation Strategy
Used StratifiedGroupKFold (n=5) to ensure:

Balanced malignant/benign distribution in each fold

Patient-level grouping to prevent data leakage

<br>
Imbalance Handling
Applied a combination of:

SMOTE (over-sampling)

Under-sampling strategies for minority/majority class balance

<br>
Magic Noise Injection
To further improve model generalization, we applied Magic Noise:
<br>

Added minimal Gaussian noise to external features (e.g., ImageNet scores)

Encourages robustness by promoting minor feature variations during training

<br>
This small trick helped reduce overfitting and improve performance on unseen data.
<br>

Evaluation Metrics
The model is evaluated using ROC AUC as the primary metric.
<br>

Calculated AUC on each fold

Final validation score is the mean AUC across folds

Final model retrained on all data before submission

Output: prediction probabilities (predict_proba) for test set

<br>
Submission Format
Final predictions were saved as submission.csv

Includes:

patient_id

prediction_score (malignancy probability)

<br>
Key Takeaways
Structured + texture + external scores = powerful fusion

Ensemble voting (with well-tuned weights) improved performance

Patient-level validation is crucial in medical settings

Tiny noise (Magic Noise) can make a measurable difference

<br>
Future Improvements
Add image features from CNNs directly (early/late fusion)

Explore multimodal training (tabular + image)

Test more sophisticated stacking/blending techniques

Integrate explainability (e.g., SHAP values) for medical interpretability

<br>
Reliable group-aware validation strategy to ensure reproducibility

<br>
