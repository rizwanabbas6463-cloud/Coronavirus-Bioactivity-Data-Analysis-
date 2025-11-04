# 🧬 Coronavirus Bioactivity Analysis using ChEMBL & RDKit

This project demonstrates a **complete cheminformatics workflow** for analyzing bioactivity data of compounds targeting the **SARS Coronavirus 3C-like proteinase** (`CHEMBL3927`) using the **ChEMBL database** and **RDKit**.  

It covers everything from **data retrieval and preprocessing** to **Lipinski descriptor computation, classification, and statistical evaluation**, enabling insights for **drug discovery** and **machine learning-based prediction**.

---

## 📖 Project Overview

This notebook performs the following key steps:

### **🔹 Phase 1: Data Retrieval and Preprocessing**
1. **Target Search** – Searches the ChEMBL database for coronavirus-related targets.  
2. **Target Selection** – Selects the *SARS-CoV 3C-like proteinase* (`CHEMBL3927`) as the main target.  
3. **Activity Retrieval** – Retrieves compound bioactivity data (IC50 values) against the selected target.  
4. **Data Cleaning** – Removes missing or invalid values to ensure clean, usable data.  
5. **Bioactivity Classification** – Categorizes compounds based on IC50:
   - **Active:** IC50 ≤ 1000 nM  
   - **Intermediate:** 1000 < IC50 < 10000 nM  
   - **Inactive:** IC50 ≥ 10000 nM  
6. **Feature Selection** – Retains key columns:
   - `molecule_chembl_id`
   - `canonical_smiles`
   - `standard_value`
7. **Data Export** – Saves both raw and preprocessed datasets:
   - `bioactivity_data.csv`
   - `bioactivity_preprocessed_data.csv`
8. **Google Drive Integration** – Automatically uploads output files to Google Drive for further use.

---

### **🔹 Phase 2: Lipinski Descriptors & Statistical Analysis**
1. **Setup RDKit** – Install and configure RDKit in Google Colab using Miniconda.  
2. **Molecular Descriptor Calculation** using RDKit:
   - Molecular Weight (MW)  
   - LogP (Octanol–Water Partition Coefficient)  
   - Number of Hydrogen Donors  
   - Number of Hydrogen Acceptors  
3. **Compute pIC50** using the formula:  
   \[
   pIC50 = -\log_{10}(IC50 \times 10^{-9})
   \]
4. **Reclassify Compounds** based on pIC50:
   - `Active`: pIC50 ≥ 6  
   - `Inactive`: pIC50 ≤ 5  
   *(Intermediate class removed)*  
5. **Exploratory Data Analysis (EDA)**:
   - Frequency distribution of bioactivity classes  
   - Scatter plot (MW vs LogP)  
   - Box plots for pIC50, MW, LogP, NumHDonors, and NumHAcceptors  
6. **Statistical Testing**:
   - Perform the **Mann–Whitney U Test** to compare descriptor distributions between active and inactive molecules.  
7. **Result Export**:
   - Export all processed data as `.csv` and visualizations as `.pdf`
   - Compress into `results.zip` for easy sharing or downstream analysis.

---

## 💻 Code Workflow Summary

```python
# Import libraries
import pandas as pd
from chembl_webresource_client.new_client import new_client

# 1️⃣ Search for coronavirus targets
target = new_client.target
target_query = target.search('coronavirus')
targets = pd.DataFrame.from_dict(target_query)

# 2️⃣ Select SARS-CoV 3C-like proteinase
selected_target = targets.target_chembl_id[6]

# 3️⃣ Retrieve activity data (IC50 values)
activity = new_client.activity
res = activity.filter(target_chembl_id=selected_target).filter(standard_type="IC50")
df = pd.DataFrame.from_dict(res)

# 4️⃣ Clean data and categorize
df2 = df[df.standard_value.notna()]
bioactivity_class = ['active' if float(i)<=1000 else 'inactive' if float(i)>=10000 else 'intermediate' for i in df2.standard_value]

# 5️⃣ Combine and export
df3 = df2[['molecule_chembl_id', 'canonical_smiles', 'standard_value']]
df3['bioactivity_class'] = bioactivity_class
df3.to_csv('bioactivity_preprocessed_data.csv', index=False)
