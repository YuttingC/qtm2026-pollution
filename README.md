# The Pollution Paradox: PM2.5 and Respiratory Health in Georgia

## Companion StoryMap
Beyond Air Quality: Reframing Respiratory Health in Georgia

**StoryMap (Interactive Version):**  
https://storymaps.arcgis.com/stories/fa3c918421c042489f78d71f85d432a5

---

## Project Overview

"If PM2.5 is driving respiratory disease in Georgia, the geography of illness should mirror the geography of pollution: the dirtiest air should produce the sickest communities."

**Does it?**

This project tests the pollution → disease hypothesis at the census-tract level across Georgia.  
The headline result:

> **Outdoor PM2.5 alone does not explain Georgia’s respiratory disease geography.**

We extend this analysis by introducing a second perspective:

> **Environmental fairness: are communities exposed to more or less pollution than expected given their socioeconomic conditions?**

---

## Research Questions Addressed Here

1. **Primary:**  
   How does long-term PM2.5 exposure relate to asthma and COPD prevalence?

2. **Secondary:**  
   Once socioeconomic context is accounted for, does PM2.5 retain explanatory power?

3. **Extension (Fairness Analysis):**  
   Are certain communities systematically over- or under-exposed relative to expectation?

---

## How to Run

### Step 1 — Set up environment

```bash
python -m venv venv
source venv/bin/activate
venv\Scripts\activate
pip install pandas numpy matplotlib seaborn scipy geopandas

```

### Step 2 - Launch Jupyter

jupyter notebook

### Step 3 - Run notebooks (in order)

1. `DataCleaning.ipynb`
2. `AnalysisCode.ipynb`

### Step 4 - Verify Outputs

After running both notebooks, your project structure should look like:

```bash
Cleaning/
├── pm25_netcdf_extracted/
├── pm25_tract_2018_2022.csv
├── socioeconomic_panel.csv
├── final_df_with_index.csv
├── pm25_11-20.csv

Cleaned/
├── merged_regression_ready.csv
├── final_merged_asthma_copd.csv

Graph/
├── Section 1 — Pollution & Health
│ ├── 1.1-GeorgiaCOPDCrudePrevalence(2018-2020).png
│ ├── 1.2-GeorgiaAsthmaCrudePrevalence(2018-2020).png
│ ├── 1.3-GeorgiaRespiratoryHealthTrends18-20.png
│ ├── 1.4-CDCAveragePM2.5(12-20).png
│ ├── 1.5-LongTermPm25andHealth.png
│ ├── 1.6-2020HealthvsSmoking.png
│ └── 1.7-2020AdjustedHealthvsPm25.png

├── Section 2 — Vulnerability & Fairness
│ ├── 2.1-UrbanRuralClassification.png
│ ├── 2.2-VulnerabilityvsPollution.png
│ ├── 2.3-VulnerabilityIndexTrend12-19.png
│ ├── 2.4-FairnessIndexTrend12-19.png
│ └── 2.5-EnvironmentalFairnessOvertime.png

├── Section 3 — Extended Analysis
│ ├── 3.1-PovertyratevsHealth18-20average.png
│ └── 3.2-FairnessindexvsHealth.png

└── Appendix
├── Appendix-cdc_vs_nasa_comparison_2018_2020.png
├── Appendix-GAPM25Mean18-22.png
├── Appendix-NASApm25Trend18-22.png
└── Appendix-NASApm25vsPoverty.png
```
## Required Packages

Install all dependencies with:

```bash
pip install pandas numpy matplotlib seaborn scipy geopandas

```
## Input Data

| File | Location | Description |
|------|----------|-------------|
| `pm25_netcdf_extracted/` | `Cleaning/` | NASA PM2.5 raw data |
| `pm25_tract_2018_2022.csv` | `Cleaning/` | Tract-level PM2.5 |
| `socioeconomic_panel.csv` | `Cleaning/` | ACS socioeconomic variables |
| `pm25_11-20.csv` | `Cleaning/` | CDC PM2.5 (2011–2020) |
| `merged_regression_ready.csv` | `Cleaned/` | Final merged dataset |
| `final_merged_asthma_copd.csv` | `Cleaned/` | Health + pollution dataset |

---

## Data Processing & What We Did

Each dataset required cleaning, transformation, and integration before analysis:

- **NASA PM2.5 (NetCDF)**
  - Extracted `.nc` files from zipped archives
  - Cropped global raster to Georgia bounding box
  - Computed **zonal mean PM2.5** for each census tract
  - Output: `pm25_tract_2018_2022.csv`

- **CDC PLACES (Asthma & COPD)**
  - Converted percentage strings (e.g., "8.7%") to numeric
  - Standardized **GEOID format (11-digit string)**
  - Merged with PM2.5 tract-level data

- **CDC Daily PM2.5 (2011–2020)**
  - Aggregated daily data into **long-term averages**
  - Used for robustness checks and long-term exposure analysis

- **ACS Socioeconomic Data**
  - Extracted variables: poverty rate, race composition, education
  - Constructed a **socioeconomic panel dataset**
  - Used as predictors in regression models

- **Final Merged Dataset**
  - Combined PM2.5 + health + socioeconomic data
  - Removed duplicates and missing observations
  - Output:
    - `merged_regression_ready.csv`
    - `final_merged_asthma_copd.csv`

## Analytic Choices

### Spatial Choices
- Unit of analysis: **Census tract**
  - Provides higher resolution than county-level analysis
- Used **TIGER/Line shapefiles (2018)**
- Applied **zonal statistics** to map raster PM2.5 → tract level

### Statistical Choices
- Primary methods:
  - **Pearson correlation**
  - **OLS regression**
- Significance level: α = 0.05
- Focused on **effect size (R², coefficients)** rather than just p-values

### Exposure Measurement
- Defined long-term exposure as:
  - **Average PM2.5 (2011–2020)**
- Motivation:
  - Chronic respiratory diseases reflect **cumulative exposure**, not short-term spikes

### Fairness Index Construction
- Defined as:
  - **Fairness Index = Expected PM2.5 − Actual PM2.5**
- Expected PM2.5 predicted using regression on socioeconomic variables
- Interpretation:
  - Positive → cleaner than expected (more fair)
  - Negative → more polluted than expected (less fair)

### Data Cleaning Decisions
- Inner join used when merging datasets
- Kept **one row per GEOID**
- Removed tracts with missing values
- Standardized all identifiers and formats

## What Didn’t Work / Failed Attempts

During the project, several approaches were tested but ultimately not used:

- **Urban vs Rural Classification (Alternative Methods)**
  - Initially attempted strict RUCA-based classification
  - Result: many intuitively urban areas classified as rural
  - Final decision: use a simpler, more interpretable classification (Urban = 1-9, Rural = 10)

- **Direct PM2.5 → Health Modeling**
  - Expected strong positive relationship
  - Result:
    - Weak or no relationship for asthma
    - Inverse relationship for COPD
  - Led to the “Pollution Paradox” insight

- **Using Daily PM2.5 Instead of Averages**
  - Attempted higher-frequency modeling
  - Result:
    - Too noisy and inconsistent with tract-level health data
  - Final approach: yearly averages

- **Including Too Many Socioeconomic Variables**
  - Early models included many predictors
  - Result:
    - Overfitting and unstable coefficients
  - Final model: reduced, interpretable variable set

- **Fairness Index as Causal Measure**
  - Initially considered as a policy score
  - Realization:
    - It is **descriptive, not causal**
  - Final interpretation adjusted accordingly

## Outputs

### Section 1 — Pollution & Health

- **1.1** Georgia COPD prevalence  
- **1.2** Georgia Asthma prevalence  
- **1.3** Respiratory trends  
- **1.4** PM2.5 distribution  
- **1.5** Long-term PM2.5 vs health  
- **1.6** Health vs smoking  
- **1.7** Adjusted health vs PM2.5  

---

### Section 2 — Vulnerability & Fairness

- **2.1** Urban vs rural classification  
- **2.2** Vulnerability vs pollution  
- **2.3** Vulnerability index trend  
- **2.4** Fairness index trend  
- **2.5** Environmental fairness over time  

---

### Section 3 — Extended Analysis

- **3.1** Poverty vs health  
- **3.2** Fairness index vs health  

---

## Method Overview

### Pollution Analysis

- Pearson correlation + OLS regression  
- Long-term PM2.5 (2011–2020 average)  

---

### Fairness Index

```bash
Fairness Index = Expected PM2.5 − Actual PM2.5
```
- Positive → more fair
- Negative → less fair

## Key Findings

### Pollution Paradox

- Weak or no link between PM2.5 and asthma  
- Inverse relationship for COPD  

### Environmental Fairness

- **Rural:** strong socioeconomic link  
- **Urban:** weaker, more fragmented  

### Temporal Insight

- **2014:** sharp fairness drop (PM2.5 spike)  
- **2020:** fairness improves (COVID-related reduction in pollution)  

---

## Limitations

- Tract-level (not individual-level data)  
- Averaged PM2.5 over time  
- Modeled health data (CDC PLACES)  
- Fairness Index is descriptive, not causal  

---

## Team

- Oscar Ni  
- Yang Lyu  
- Yuting Chen  

---

## License

GPL-3.0  

---

## Citation

Lyu, Y., Ni, O., & Chen, Y. (2026).  
*Beyond Air Quality: Reframing Respiratory Health in Georgia*
