# 🧬 Coronavirus & Acetylcholinesterase Bioactivity Analysis using ChEMBL, RDKit & Machine Learning

This project demonstrates a **complete cheminformatics and bioactivity modeling pipeline** — from retrieving biological data using **ChEMBL**, processing it with **RDKit**, and applying **machine learning** (Random Forest) to classify compound activity.

It integrates three major phases:
1. **Bioactivity Data Retrieval** from ChEMBL (SARS-CoV Target)  
2. **Molecular Descriptor & Statistical Analysis** using RDKit  
3. **Bioactivity Prediction Model** using Machine Learning on Acetylcholinesterase data  

---

## 🧠 Project Overview

### 🔹 **Phase 1: ChEMBL Coronavirus Bioactivity Retrieval**

This phase retrieves and processes compound bioactivity data targeting the **SARS-CoV 3C-like proteinase (CHEMBL3927)**.

#### Steps:
1. **Search for Coronavirus Targets** in the ChEMBL database  
2. **Select SARS-CoV 3C-like proteinase (`CHEMBL3927`)**  
3. **Retrieve compound bioactivities (IC50 values)**  
4. **Clean and filter** missing or invalid data  
5. **Classify compounds** based on IC50:
   - Active: IC50 ≤ 1000 nM  
   - Intermediate: 1000 < IC50 < 10000 nM  
   - Inactive: IC50 ≥ 10000 nM  
6. **Save clean datasets**:
   - `bioactivity_data.csv`
   - `bioactivity_preprocessed_data.csv`

#### Example Code:
```python
from chembl_webresource_client.new_client import new_client
import pandas as pd

target = new_client.target
target_query = target.search('coronavirus')
targets = pd.DataFrame.from_dict(target_query)

selected_target = targets.target_chembl_id[6]
activity = new_client.activity
res = activity.filter(target_chembl_id=selected_target).filter(standard_type="IC50")

df = pd.DataFrame.from_dict(res)
df2 = df[df.standard_value.notna()]
bioactivity_class = ['active' if float(i)<=1000 else 'inactive' if float(i)>=10000 else 'intermediate' for i in df2.standard_value]

df3 = df2[['molecule_chembl_id', 'canonical_smiles', 'standard_value']]
df3['bioactivity_class'] = bioactivity_class
df3.to_csv('bioactivity_preprocessed_data.csv', index=False)
