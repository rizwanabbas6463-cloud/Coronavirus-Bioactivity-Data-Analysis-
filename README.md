# 🧬 Coronavirus Bioactivity Data Analysis using ChEMBL

This project retrieves, processes, and classifies bioactivity data for compounds targeting the **SARS Coronavirus 3C-like proteinase** (CHEMBL3927) using the **ChEMBL database**.  
It demonstrates how to collect, clean, and prepare biological activity data for further **machine learning or drug discovery** analysis.

---

## 📖 Project Overview

This notebook performs the following main tasks:

1. **Target Search** – Searches the ChEMBL database for coronavirus-related targets.  
2. **Target Selection** – Selects the SARS-CoV 3C-like proteinase (`CHEMBL3927`) as the primary target.  
3. **Activity Retrieval** – Retrieves compound bioactivity data (IC50 values) against the selected target.  
4. **Data Cleaning** – Removes missing or invalid values to ensure clean, usable data.  
5. **Bioactivity Classification** – Categorizes compounds as:
   - **Active:** IC50 ≤ 1000 nM  
   - **Intermediate:** 1000 < IC50 < 10000 nM  
   - **Inactive:** IC50 ≥ 10000 nM  
6. **Feature Selection** – Selects key columns:
   - `molecule_chembl_id`
   - `canonical_smiles`
   - `standard_value`
7. **Data Export** – Saves both raw and preprocessed data to CSV files:
   - `bioactivity_data.csv`
   - `bioactivity_preprocessed_data.csv`
8. **Google Drive Integration** – Automatically saves outputs to Google Drive for storage or further use.

---

## 📦 Files Generated

| File Name | Description |
|------------|-------------|
| `bioactivity_data.csv` | Raw bioactivity data retrieved from ChEMBL |
| `bioactivity_preprocessed_data.csv` | Cleaned and classified bioactivity dataset |

---

## 🧰 Tools & Libraries Used

- **Python 3**
- **Pandas** – For data manipulation and analysis  
- **ChEMBL WebResource Client** – To fetch data from the ChEMBL database  
- **Google Colab** – For running and saving notebooks  
- **Google Drive** – To store and organize dataset outputs  

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
