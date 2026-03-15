# /data

This folder contains all data used in this project — both the original raw source files and the cleaned versions that are ready for analysis.

---

## Folder Structure

```
/data
  /raw_data
    /2022/      # HOBO logger CSVs from the 2022 deployment season
    /2023/      # HOBO logger CSVs from the 2023 deployment season
    /2024/      # HOBO logger CSVs + lab sample results from the 2024 season
    /2025/      # HOBO logger CSVs + lab sample results from the 2025 season
  /cleaned_data
    Master_Logger_Daily_2022_2025.csv   # All logger years merged and cleaned
    Sample_results_2024_2025.csv        # Lab sample results from 2024 and 2025, cleaned and combined
```

---

## Raw Data Sources

### 1. AquaFlux Device Logger (`/raw_data/20XX/`)

**What it is:** Continuous water quality readings recorded by the AquaFlux EMF device while it is deployed seasonally in Chrysler Pond. The device logs data at regular intervals throughout each growing season (roughly May–October).

**Format:** `.csv` files exported from HOBOware software. Each year's data arrived as multiple monthly files, which are later merged in Notebook 01.

**Years covered:** 2022, 2023, 2024, 2025

**Key columns in raw files:**

| Column Position | Contents |
|---|---|
| 0 | HOBO row counter (not used) |
| 1 | Timestamp |
| 2 | Dissolved oxygen (mg/L) |
| 3 | Water temperature (°F) |

> Note: Raw files have two header rows before the data begins. Row 1 is a plot title; Row 2 is column labels. Both are skipped during processing.

---

### 2. Lab Sample Results (`/raw_data/2024/` and `/raw_data/2025/`)

**What it is:** Water and sediment grab samples sent to external labs for chemical analysis. Samples were collected at two locations: near the EMF device and at the pond center.

**Format:** PDF lab reports manually transcribed into CSV. One CSV per year.

**Files:**
- `2024/2024_Sample_Results.csv`
- `2025/2025_Sample_Results.csv`

**Columns — 2024 file:**

| Column | Description |
|---|---|
| Date | Sample collection date |
| Client Sample ID | Sample location label (e.g., "EMF - Water", "Center - Sludge Top") |
| Matrix | Sample type: WATER or SLUDGE |
| Parameter | The substance being measured (e.g., Total Phosphorus, E. coli) |
| Value | Measured value. "ND" = not detected |
| Units | Unit of measurement (mg/L, µg/g-dry, MPN/100mL, wt%) |

**Columns — 2025 file:**

| Column | Description |
|---|---|
| Sampling Date | Sample collection date |
| Sample Label | "Shallow Sample" (5 ft depth) or "Deep Sample" (7 ft depth) |
| Depth (ft) | Sampling depth in feet |
| Parameter | The substance being measured |
| Value | Measured value. "<10" indicates below the detection threshold |
| Units | Unit of measurement (µg/L, NTU, µS/cm, mg/L) |

> **Important note on 2024 depth:** The 2024 lab report did not include sampling depth. Depths were inferred from context: WATER samples = 5 ft, Sludge Top samples = 7 ft, Sludge Bottom samples = 10 ft.

> **Important note on comparability:** The 2024 and 2025 samples were collected by different labs using different protocols. The parameter names, units, and measurement methods do not align perfectly between years. Direct year-over-year comparisons should be interpreted with caution. See the main README for a full discussion of limitations.

---

## Cleaned Data

### Master_Logger_Daily_2022_2025.csv

**What it is:** All four years of HOBO logger data merged into one file, with low-quality readings removed.

**Cleaning steps applied:**
- Skipped the two HOBO header rows
- Renamed columns to: `Timestamp`, `DO_mgL`, `Temp_F`, `Year`
- Converted timestamps and numeric columns; coerced unreadable values to NaN
- Dropped rows with unparseable timestamps
- Removed physically impossible readings (DO ≤ 0 or > 20 mg/L; Temp ≤ 32°F or ≥ 95°F) — these indicate the sensor was likely out of the water

**Columns:**

| Column | Description |
|---|---|
| Timestamp | Date and time of measurement (datetime) |
| DO_mgL | Dissolved oxygen in milligrams per liter |
| Temp_F | Water temperature in degrees Fahrenheit |
| Year | Deployment year (2022–2025) |

---

### Sample_results_2024_2025.csv

**What it is:** The 2024 and 2025 lab sample results merged into a single standardized table.

**Cleaning steps applied:**
- Renamed columns for consistency across years
- Assigned Zone labels: "Near Device" (near EMF unit) and "Pond Center"
- Inferred depth for 2024 samples (see note above)
- Inferred Matrix (WATER/SLUDGE) for 2025 samples based on depth
- Converted "ND" (not detected) and "<10" values to 0.0 for numeric analysis
- Added `Standard_Value` and `Standard_Units` columns: water sample values in mg/L were converted to µg/L for comparability
- Normalized parameter names (e.g., "Total Phosphorus (as P)" → "Total Phosphorus")

**Columns:**

| Column | Description |
|---|---|
| Year | Sample year (2024 or 2025) |
| Sampling Date | Date samples were collected |
| Zone | Sampling location: "Near Device" or "Pond Center" |
| Sample Label | Original sample label from the lab report |
| Matrix | WATER or SLUDGE |
| Depth (ft) | Sampling depth in feet |
| Parameter | Substance measured (e.g., Total Phosphorus, Chlorophyll a) |
| Value | Original reported value (may be "ND" or "<10") |
| Units | Original units from the lab report |
| Standard_Value | Numeric value standardized for analysis (ND = 0.0) |
| Standard_Units | Unit after standardization |

---

## Ethical Considerations

- **Conflict of interest:** This project was conducted in partnership with AquaFlux Technology, the supplier of the device being evaluated. To mitigate this, Sentinel-2 satellite imagery — a fully independent, publicly available data source — was used as the primary evidence base. All findings, including those that do not support device efficacy, are reported transparently.
- **Privacy:** All data pertains to water quality at a privately used pond. No personally identifiable information was collected or used.
- **Bias:** The lab sampling protocol changed between 2024 and 2025, and sampling was conducted at different times of the growing season. These inconsistencies limit the conclusions that can be drawn from the lab data. See the main README for full discussion.
