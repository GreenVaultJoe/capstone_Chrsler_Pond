# Evaluating EMF-Based HAB Suppression at Chrysler Pond
### Yale Environmental Data Science Certificate Program - Capstone Project 2025-2026

---

## Project Overview

Harmful Algal Blooms (HABs) are a serious environmental and public health concern for Chrysler Pond in Copake, NY. Certain algae species produce toxins that can harm humans, pets, and aquatic life, and unchecked bloom growth can result in fish kills, foul odors, and a complete loss of recreational use of the pond. Residents who swim, fish, and share stewardship of this water body have a direct stake in its health.

Since 2022, an electromagnetic frequency (EMF) device manufactured by AquaFlux Technology has been seasonally deployed in Chrysler Pond with the goal of suppressing HAB formation by altering the water chemistry in a way that makes it harder for algae to grow and flourish. This project asks: **can we find evidence in the data that this intervention has worked?**

To answer this, I combined three independent data sources: a continuous water quality logger, lab sample results, and Sentinel-2 satellite imagery to assess water quality conditions before and after the device's deployment.

---

## Research Question

> Has the seasonal deployment of an EMF device at Chrysler Pond (Copake, NY) measurably reduced harmful algal bloom activity between 2022 and 2025, as detectable through satellite-derived chlorophyll indices, visible greenness, and water quality measurements?

---

## Audience

This project is intended for residents who have access to and/or share responsibility for Chrysler Pond, as well as for the broader environmental data science community interested in low-cost freshwater monitoring methods. It is also submitted as a capstone project for the Yale School of the Environment's Environmental Data Science Certificate Program.

---

## Repository Structure

```
/data
  /raw_data              # Original logger CSVs, lab sample PDFs, unprocessed data
  /cleaned_data          # Standardized and merged datasets ready for analysis
/notebooks          # Jupyter notebooks for each analysis phase
/communication      # Slide deck and presentation materials
README.md
requirements.txt
```

---

## Data Sources

| Source | Description | Format | Years |
|--------|-------------|--------|-------|
| AquaFlux Device Logger | Continuous water quality measurements (temperature, dissolved oxygen, pH, etc.) recorded seasonally while device is deployed | `.hobo` -> `.csv` | 2022-2025 |
| Lab Sample Results | Water quality grab samples analyzed by external laboratories | PDF -> `.csv` | 2024, 2025 |
| Sentinel-2 Satellite Imagery | European Space Agency multispectral satellite imagery accessed via Google Earth Engine Python API | Cloud-filtered GeoTIFF / extracted time series | 2017-2025 |
| NOAA / Open-Meteo Weather | Daily temperature and precipitation for West Copake, NY used as confounding variable check | API / `.csv` | 2017-2025 |

A data dictionary for each dataset is included in `/data/README.md`.

---

## Methods & Process

### Phase 1 - Data Collection and Organization
Logger data was downloaded in `.hobo` format using HOBOware software and exported to CSV. Lab sample results were manually transcribed from PDF reports into structured CSVs with columns for date, parameter, value, unit, and method. Because each year's logger data arrived as multiple monthly files, a combining script was written to merge them into a single seasonal dataset per year.

### Phase 2 - Cleaning and Standardization
Units and timestamps were standardized across datasets. Lab results from 2024 and 2025 were collected by different companies using different protocols, requiring careful manual alignment. Best efforts were made to normalize units, though some parameters remain non-comparable across years.

### Phase 3 - Exploratory Data Analysis
Initial EDA was conducted on both the logger and lab sample datasets to identify patterns, gaps, and anomalies. This phase revealed significant limitations in the lab data (see Limitations), which motivated a pivot toward satellite imagery as the primary analytical method.

### Phase 4 - Satellite Imagery Analysis
Sentinel-2 Level-2A (surface reflectance) imagery was accessed via the Google Earth Engine Python API. Two independent metrics were derived for the pond location across 95 cloud-filtered scenes (May-October, 2017-2025):

- **NDCI (Normalized Difference Chlorophyll Index)** - computed as (Band 5 - Band 4) / (Band 5 + Band 4). NDCI values above 0.1 are used as a bloom threshold indicator.
- **Visible Greenness (Green Band Ratio)** - computed as Band 3 / (Band 2 + Band 3 + Band 4). Values above 0.38 indicate algal tinting of the water surface.

Scenes with greater than 10% cloud cover were excluded. Values below -0.3 were flagged as likely cloud shadow or ice artifacts and removed (5 scenes total, all in late October).

### Phase 5 - Confounding Variable Analysis
Summer temperature and precipitation data (May-October) were retrieved from Open-Meteo for each year 2017-2025. Pearson correlations were computed between weather variables and bloom frequency to assess whether climate conditions independently explain observed variation.

### Phase 6 - Before/After Comparison
An informal interrupted time series framework was used, comparing pre-intervention (2017-2021) and post-intervention (2022-2025) periods across all metrics.

---

## Key Findings

Satellite-derived NDCI and visible greenness data do not show consistent bloom suppression following EMF device deployment. Post-intervention years show a higher frequency of scenes exceeding the bloom threshold (16 vs. 5 pre-intervention). However, 2022 and 2025 both record among the lowest chlorophyll values in the entire 8-year satellite record, while 2024 - with confirmed device operation from April through September - is the worst bloom year on record (6 scenes above threshold, peak NDCI of 0.24).

Temperature shows a moderate correlation with bloom frequency (r = 0.53) but this relationship is not statistically significant at p < 0.05, likely due to the small sample size (n = 9 years). Precipitation shows no meaningful correlation (r = -0.09). Critically, 2022 and 2024 had nearly identical summer weather conditions but dramatically different bloom outcomes, which weather alone cannot explain.

**Overall conclusion:** Satellite evidence is insufficient to confirm device efficacy. Results are inconclusive and warrant longer-term monitoring, a comparison site, and more consistent sampling.

---

## Assumptions

**Lab Sample Data**
- The 2024 lab report did not specify sampling depth for all samples. Depth was inferred using contextual clues within the report regarding standard sampling protocols.
- It is assumed that samples were not necessarily collected at the exact same location within the pond across years, as no GPS coordinates were recorded. Comparative analysis between years proceeds with this limitation acknowledged.
- Chlorophyll-a was only measured in 2025 samples, not 2024, which significantly limits direct lab-based before/after comparison and is a primary reason satellite imagery was elevated to the main analytical method.

**Satellite Imagery**
- The pond center coordinate (-73.60508, 42.10069) was used consistently across all scenes. A 50-meter buffer was applied to reduce edge pixel contamination.
- Scenes with NDCI values below -0.3 were treated as non-representative of true water quality conditions (cloud shadow, ice, or mixed pixels) and excluded.
- Measuring visible greenness from satellite imagery involves some subjectivity; remote sensing indicators may not perfectly match real bloom conditions on the ground.

**Device Deployment**
- The device was not deployed or retrieved on the same calendar date every year. Analysis treats the full May–October growing season as the relevant window for all years, which may slightly misrepresent device-on vs. device-off conditions in shoulder months.

**General**
- No pre-deployment lab sample data exists. Lab-based before/after comparison is therefore not possible and satellite imagery serves as the sole source of pre-2022 baseline.
- PDF lab reports may contain transcription inconsistencies or unit irregularities. Best efforts were made to standardize these, but errors cannot be ruled out entirely.

---

## Limitations

- **No control site.** Without a comparable nearby pond with no EMF device, it is impossible to rule out regional environmental factors as drivers of observed change.
- **Small sample size.** Nine growing seasons is insufficient for robust statistical inference. Significance thresholds are difficult to reach and results should be interpreted with caution.
- **Inconsistent lab sampling.** Different labs, different parameters, different timing, and no pre-deployment baseline severely constrain what the lab data can tell us.
- **Satellite resolution.** At 10-meter resolution, pond edge pixels may mix water and land signals. The 50-meter buffer mitigates this but does not eliminate it.
- **Unknown confounders.** Other changes in the pond's watershed between 2017 and 2025 - land use, runoff, waterfowl activity, aquatic vegetation - are not accounted for.

---

## Ethical Considerations

This project was conducted in partnership with AquaFlux Technology, the supplier of the device being evaluated. This relationship introduces a potential conflict of interest. To mitigate this, Sentinel-2 satellite imagery, a fully independent, publicly available data source with no connection to AquaFlux was used as the primary evidence base. All findings, including those that do not support device efficacy, are reported transparently.

Water quality data from this project pertains to a privately used pond. No personally identifiable information was collected or used.

---

## How to Reproduce

```bash
# Clone the repo
git clone https://github.com/[your-username]/[repo-name].git

# Install dependencies
pip install -r requirements.txt

# Authenticate Google Earth Engine (required for satellite notebooks)
earthengine authenticate

# Run notebooks in order
notebooks/01_Logger_Data_Merge_and_clean.ipynb
notebooks/02_Sample_Data_Cleaning_and_EDA.ipynb
notebooks/03_EDA_on_cleaned_data.ipynb
notebooks/04_Satellite_NDCI_analysis.ipynb #this notebook includes weather analysis
notebooks/05_Satelite_visible_greenness.ipynb
```

Note: Google Earth Engine access requires a free account registered at earthengine.google.com. Use project ID `yale-capstone-hab` or substitute your own registered project.

---

## Tools & Libraries

- Python 3.12
- Google Earth Engine Python API (`earthengine-api`, `geemap`)
- `pandas`, `numpy`, `matplotlib`, `scipy`
- `openmeteo-requests` (weather data)
- Sentinel-2 Level-2A imagery (Copernicus/ESA, accessed via GEE)

See `requirements.txt` for full dependency list.

---

## Citation

**NDCI Methodology:**
Mishra, S., & Mishra, D. R. (2012). Normalized difference chlorophyll index: 
A novel model for remote estimation of chlorophyll-a concentration in turbid 
productive waters. *Remote Sensing of Environment, 117*, 394-406. 
https://doi.org/10.1016/j.rse.2011.10.016

**NDCI Sentinel-2 Implementation:**
Sentinel Hub Custom Scripts. Normalized Difference Chlorophyll Index (NDCI). 
European Space Agency. 
https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/ndci/

**Sentinel-2 Imagery:**
Sentinel-2 imagery courtesy of the European Space Agency (ESA) and European 
Commission, accessed via Google Earth Engine.

**Weather Data:**
Open-Meteo (open-meteo.com), sourced from NOAA and ERA5 reanalysis.
