# Predictive Maintenance for Industrial Equipment (AI4I 2020)

## English

### Overview
This project builds a **predictive maintenance** workflow using the **AI4I 2020** synthetic milling machine dataset. The notebook covers **data preprocessing, feature engineering, exploratory analysis, and a baseline machine learning model** to predict whether a machine failure occurs.

### Dataset
The dataset contains **10,000 rows** and **14 original columns** describing operating conditions and failure labels (AI4I-style synthetic data):

- **Identifiers**: `UDI`, `Product ID`, `Type` (L/M/H quality variant)
- **Sensors / process parameters**: air/process temperature, rotational speed, torque, tool wear
- **Labels**:
  - `Machine failure` (binary label in the raw dataset)
  - Failure modes: `TWF`, `HDF`, `PWF`, `OSF`, `RNF`

In the notebook, a categorical label **`Machine failure Labelled`** is created based on rule checks for each failure mode (or `"No Failure"` if none apply).

### What the notebook does
`project.ipynb` implements:

- **Preprocessing / feature engineering**
  - Normalizes temperature columns into:
    - `Normalized_air_temp`
    - `Normalized_process_temp`
  - Creates a derived feature **`Power [W]`** from torque and rotational speed.
  - Removes outliers (notebook run shows **9836** rows after filtering).
- **Encoding**
  - One-hot encodes:
    - `Product Type` → `Product Type_H`, `Product Type_L`, `Product Type_M`
    - `Machine failure Labelled` → many one-hot columns (including multi-failure combinations)
- **Binary target for ML**
  - Creates **`Machine Failure`** (0/1) by taking an OR across all one-hot failure-mode columns (excluding `"No Failure"`).
  - Notebook counts: **0 → 7679**, **1 → 2157**.
- **Model training**
  - Splits data into **train / validation / test**:
    - train: 70%
    - validation: 15%
    - test: 15%
  - Trains **Logistic Regression** and evaluates on the validation set.
  - Runs **GridSearchCV (5-fold)** to tune hyperparameters.

### Model results (from the notebook outputs)
- **Baseline Logistic Regression (validation)**:
  - Accuracy: **0.8725**
  - Class-wise report (validation):
    - class 0: precision **0.89**, recall **0.95**, f1 **0.92**
    - class 1: precision **0.78**, recall **0.63**, f1 **0.70**
- **GridSearchCV (5-fold)**:
  - Best hyperparameters:
    - `{'C': 0.1, 'max_iter': 1000, 'penalty': 'l1', 'solver': 'liblinear', 'tol': 0.001, 'verbose': 4}`
  - Best CV accuracy: **0.8778**
  - Cross-validated means (with best model):
    - Accuracy: **0.8776**
    - Precision: **0.7758**
    - Recall: **0.6240**
    - F1-score: **0.6898**

### Files in this repo
- **`project.ipynb`**: end-to-end workflow (EDA → preprocessing → modeling)
- **`ai4i2020.csv`**: raw dataset (AI4I 2020-style synthetic data)
- **`preprocessed_data.csv`**: preprocessed dataset (after feature engineering + outlier removal)
- **`preprocessed_final.csv`**: cleaned dataset used before encoding
- **`preprocessed_encoded.csv`**: one-hot encoded dataset
- **`basic_df.csv`**: final ML table used for training (features + `Machine Failure`)

### How to run
1. **Create a Python environment** (recommended): Python **3.11** (the notebook kernel shows **3.11.9**).
2. **Install dependencies**:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

3. **Fix the dataset path**
   - The notebook reads/writes files under a `data/` folder (e.g. `data/ai4i2020.csv`).
   - In this repository snapshot, the CSVs are in the project root.

Choose one:
- **Option A (recommended)**: create a `data/` folder and move/copy the CSVs into it.
- **Option B**: edit `project.ipynb` paths from `data/<file>.csv` to `<file>.csv`.

4. **Run the notebook**
   - Open `project.ipynb` in Jupyter / VS Code and run cells top-to-bottom.

### Notes / next steps
- The target is **imbalanced** (many more “No Failure” rows), so accuracy alone can be misleading. Consider adding ROC-AUC/PR-AUC, class weights, or resampling.
- You can extend the project by trying tree-based models (Random Forest / Gradient Boosting), calibration, and feature importance reporting.

---

## മലയാളം (Malayalam)

### അവലോകനം
ഈ പ്രോജക്ട് **AI4I 2020** (synthetic milling machine) ഡാറ്റാസെറ്റ് ഉപയോഗിച്ച് **predictive maintenance** പൈപ്പ്‌ലൈൻ നിർമ്മിക്കുന്നു. `project.ipynb`-ൽ **preprocessing, feature engineering, EDA, modeling** എന്നിവ നടപ്പാക്കിയിട്ടുണ്ട്.

### ഡാറ്റാസെറ്റ്
ഡാറ്റാസെറ്റിൽ **10,000 rows** ഉണ്ട്. പ്രധാന കോളങ്ങൾ:

- **Identifiers**: `UDI`, `Product ID`, `Type` (L/M/H)
- **Process / sensor features**: temperature, rotational speed, torque, tool wear
- **Labels**:
  - `Machine failure` (binary)
  - Failure modes: `TWF`, `HDF`, `PWF`, `OSF`, `RNF`

Notebook-ിൽ rule-based ആയി **`Machine failure Labelled`** എന്ന categorical label ഉണ്ടാക്കുന്നു (failure ഇല്ലെങ്കിൽ `"No Failure"`).

### Notebook ചെയ്യുന്നതെന്ത്?
- **Preprocessing / feature engineering**
  - `Normalized_air_temp`, `Normalized_process_temp` എന്ന normalized temperature features
  - torque + rotational speed ഉപയോഗിച്ച് **`Power [W]`** എന്ന derived feature
  - outlier removal (notebook run-ൽ **9836 rows**)
- **Encoding**
  - `Product Type`യും `Machine failure Labelled`യും one-hot encode ചെയ്യുന്നു
- **Binary target**
  - one-hot failure-mode columns-ൽ OR എടുത്ത് **`Machine Failure` (0/1)** സൃഷ്ടിക്കുന്നു
  - Notebook count: **0 → 7679**, **1 → 2157**
- **Modeling**
  - train/validation/test split: **70% / 15% / 15%**
  - **Logistic Regression** train ചെയ്ത് validation-ൽ evaluate ചെയ്യുന്നു
  - **GridSearchCV (5-fold)** ഉപയോഗിച്ച് hyperparameter tuning

### Model ഫലങ്ങൾ (Notebook outputs)
- Validation accuracy: **0.8725**
- Best GridSearch hyperparameters:
  - `{'C': 0.1, 'max_iter': 1000, 'penalty': 'l1', 'solver': 'liblinear', 'tol': 0.001, 'verbose': 4}`
- Best CV accuracy: **0.8778**
- CV mean metrics: Accuracy **0.8776**, Precision **0.7758**, Recall **0.6240**, F1 **0.6898**

### Run ചെയ്യുന്നത്
1. Python **3.11** environment setup ചെയ്യുക  
2. Packages install ചെയ്യുക:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

3. Notebook-ിൽ `data/` path ഉപയോഗിക്കുന്നതിനാൽ:
   - **Option A**: `data/` folder create ചെയ്ത് CSV files അതിലേക്ക് move/copy ചെയ്യുക  
   - **Option B**: notebook-ിലെ `data/<file>.csv` paths `<file>.csv` ആയി മാറ്റുക  
4. `project.ipynb` open ചെയ്ത് cells run ചെയ്യുക.

