# /notebooks

Run notebooks in order — each one builds on the outputs of the ones before it.

---

## Setup

```bash
pip install -r requirements.txt
```

Notebooks 04 and 05 also require a Google Earth Engine account:

```bash
earthengine authenticate
```

Register at [https://code.earthengine.google.com/register](https://code.earthengine.google.com/register). Replace the project ID `yale-capstone-hab` in `ee.Initialize()` with your own if needed.

---

## Notebooks

| Notebook | What it does |
|---|---|
| **01_Logger_Data_Merge_and_clean** | Loads all raw HOBO logger CSVs from GitHub, removes bad readings, and merges everything into one clean file |
| **02_Sample_Data_Cleaning_and_EDA** | Loads the 2024 and 2025 lab sample CSVs, standardizes columns and units, and produces basic charts |
| **03_EDA_on_cleaned_data** | Exploratory analysis on the cleaned logger and sample data — time series plots, parameter comparisons |
| **04_Satellite_NDCI_analysis** | Pulls Sentinel-2 satellite imagery via Google Earth Engine, computes NDCI (a chlorophyll index), and compares bloom activity before and after device deployment. Also includes weather correlation analysis |
| **05_Satellite_visible_greenness** | Same imagery, different metric — computes visible greenness as an independent cross-check on the NDCI findings |
