# 🧬 Computational Drug Discovery Project  
### Predicting Bioactivity of Acetylcholinesterase Inhibitors using Machine Learning   
**Course:** Bioinformatics Project  
**Project Type:** Computational Drug Discovery  
**Tools:** Python, scikit-learn, pandas, seaborn, RDKit  

## 📖 Overview  

This project focuses on **computational drug discovery** targeting the **Acetylcholinesterase (AChE) enzyme**, which plays a critical role in Alzheimer’s disease. The main goal is to identify and model bioactive compounds capable of inhibiting this enzyme using cheminformatics and machine learning techniques.

The project consists of the following four parts:

| Part | Title | Description |
| **1** | Data Collection | Retrieve bioactivity data of acetylcholinesterase inhibitors from ChEMBL database |
| **2** | Data Preprocessing | Process and clean molecular data; calculate molecular descriptors |
| **3** | Exploratory Data Analysis | Analyze molecular features and bioactivity class distributions |
| **4** | Regression Modeling | Build a Random Forest regression model to predict pIC50 values |

---

## 🧩 Part 1 — Bioactivity Data Collection  

The dataset was obtained from the **ChEMBL Database** using Python scripts to fetch compounds tested against the target **Acetylcholinesterase (AChE)**.

**Data Columns:**
- `molecule_chembl_id`
- `canonical_smiles`
- `standard_value`
- `standard_units`
- `pIC50` (calculated bioactivity measure)

After cleaning, the dataset was saved as:  
`acetylcholinesterase_01_bioactivity_data_raw.csv`

---

## ⚙️ Part 2 — Data Preprocessing & Descriptor Calculation  

**Objective:** Convert chemical structures (SMILES) into machine-readable molecular fingerprints.

### Steps:
1. Remove missing values and invalid entries.  
2. Calculate **PubChem molecular fingerprints (881 binary features)** using RDKit.  
3. Merge these descriptors with the target variable `pIC50`.

**Final dataset:**  
`acetylcholinesterase_06_bioactivity_data_3class_pIC50_pubchem_fp.csv`

---

## 📊 Part 3 — Exploratory Data Analysis (EDA)  

We analyzed the distribution of `pIC50` values to classify compounds into:
- **Active** (pIC50 ≥ 6)
- **Intermediate** (5 ≤ pIC50 < 6)
- **Inactive** (pIC50 < 5)

Visualization examples:
```python
sns.histplot(df['pIC50'], kde=True)
plt.title('Distribution of pIC50 Values')
