# Network Anomaly Detection System

## Overview
This project implements an AI-powered network anomaly detection system using the UNSW-NB15 dataset.  
The goal is to learn patterns of **normal network traffic** and detect **malicious or anomalous flows** using unsupervised machine learning techniques.

The system is designed to resemble real-world Intrusion Detection Systems (IDS), where labeled attack data is not assumed to be available during training.

---

## Dataset
- **Dataset**: UNSW-NB15
- **Each row** represents a network flow summarizing packet-level communication.
- **Features include**:
  - Flow duration, packet counts, byte counts
  - Timing and jitter statistics
  - TCP behavior and connection statistics
  - Protocol and service metadata

### Labels (used only for evaluation)
- `label = 0` → Normal traffic  
- `label = 1` → Attack traffic  
- `attack_cat` → Type of attack (DoS, Reconnaissance, Exploits, etc.)

⚠️ Labels are **never used during training** to avoid data leakage.

---

## Preprocessing
1. Dropped non-feature columns:
   - `id`, `attack_cat`, `label`
2. One-hot encoded categorical features:
   - `proto`, `service`, `state`
3. Handled missing and infinite values using median imputation
4. Standardized features using `StandardScaler`
5. Trained models **only on normal traffic (`label = 0`)**

---

## Model: Isolation Forest

### Training Strategy
- Trained exclusively on benign traffic to model normal behavior
- Anomalies are detected based on deviation from this learned baseline

### Hyperparameter Tuning
- Performed cross-validation to tune:
  - `n_estimators`
  - `contamination`
- ROC-AUC used as the evaluation metric on validation folds

**Final configuration**:
```python
IsolationForest(
    n_estimators=200,
    contamination=0.01,
    random_state=42,
    n_jobs=-1
)
