# DSCI410-Research-Project
Class based research project studying CAHOOTS in Eugene,OR. 
# Project Methods Description

## 1. Research Questions

- **RQ8**: What total proportion of calls are handled by each agency (CAHOOTS vs. Police)?
- **RQ10**: What proportion of calls are diverted from police to CAHOOTS response? How has that proportion changed over time?

These questions aim to evaluate how CAHOOTS has affected emergency response patterns in Eugene, particularly after its January 2017 expansion and its April 2024 service halt.

---

## 2. Data Description

### 2.1. Computer-Aided Dispatch (CAD) Data
- **Source**: Public records request from the City of Eugene Police Department
- **Observations**: 1.48 million individual 911 call records from 2014 to 2025
- **Key Variables**: 
  - `calltime` (timestamp)
  - `nature` (incident type)
  - `primeunit` (responder)
  - `callsource`, `priority`, `zip`
- **Strengths**: Longitudinal coverage across agencies and call types
- **Limitations**: Inconsistencies in `nature` field, missing unit IDs in early records, no dispatcher notes

### 2.2. CAHOOTS Responder Classification
- **Source**: Derived from known unit IDs (e.g., 1J77, CAHOOT, etc.)
- **Method**: Regex matching on `primeunit` to classify each call as `CAHOOTS`, `NOT CAHOOTS`, or `unknown`
- **Limitations**: Some unit types are ambiguous or missing; classification errs on the side of caution

---

## 3. Data Cleaning and Processing Steps

- Concatenated 12 CSVs using `glob` into a single DataFrame
- Parsed `calltime` column as datetime with error handling
- Created `Unit Type` column from `primeunit` using regex
- Merged duplicate `nature` labels (e.g., "CHECK WELFARE, CAHOOTS" → "CHECK WELFARE")
- Created new features: `hour`, `year`, `month_num`
- Filtered to keep only incident types where CAHOOTS had substantial or shared involvement

**Script Locations**:
- `data_prep.ipynb` handles all loading and preprocessing
- README files explain how to run each cell and modify classification logic

---

## 4. Analytical Steps

| Step                         | Methodology                            | Input Data       | Output                      |
|------------------------------|----------------------------------------|------------------|-----------------------------|
| Classify Responder Type      | Regex on `primeunit`                   | `master_df`      | `Unit Type` column          |
| Filter CAHOOTS-relevant calls| Threshold-based categorization         | `master_df`      | `core_cahoots_df`           |
| Merge label duplicates       | `.replace()` with dictionary mapping   | `core_cahoots_df`| Standardized `nature`       |
| Analyze call timing          | Groupby `hour`, `year`, `Unit Type`    | `core_cahoots_df`| Time-of-day distributions   |
| Early-morning focus (3–10am) | Filter on `hour` range and group       | `core_cahoots_df`| Yearly CAHOOTS share graphs |
| Visualization                | `seaborn`, `matplotlib`                | grouped outputs  | Bar/line plots by year/nature|

---

## 5. Scripts and Documentation

- Scripts are organized into one notebook
  -  Currently named `v1.ipynb` , and will be updated as I work on the project
>
> 
## 6. Current readme.md

# CAHOOTS Dispatch Analysis – Project README

This project analyzes dispatch records from Eugene’s 911 system between 2014 and 2025 to understand changes in emergency call patterns, with a focus on CAHOOTS (Crisis Assistance Helping Out On The Streets).

All code and analysis are contained in a single notebook:

## File

- `v1.ipynb`: 
  - Loads and concatenates yearly CAD CSV files (2014–2025)
  - Cleans and parses the `calltime` column into usable datetime format
  - Uses regex classification on `primeunit` to identify CAHOOTS vs. NOT CAHOOTS responders
  - Merges overlapping `nature` values (e.g. “CHECK WELFARE” and “CHECK WELFARE, CAHOOTS”)
  - Classifies `nature` types by who primarily responds (CAHOOTS vs NOT CAHOOTS)
  - Filters down to relevant `nature` types
  - Produces time-of-day histograms and year-by-month CAHOOTS share plots for the 3–10am window

## How to Run

1. Ensure all CAD data files are in the `CAD_data_through_2025/` folder
2. Open `v1.ipynb` in Jupyter or VS Code
3. Run each cell top to bottom to execute cleaning, analysis, and plotting

## Requirements

- Python 3.9+
- pandas
- numpy
- matplotlib
- seaborn
- glob
- re

To install requirements:

```bash
pip install pandas numpy matplotlib seaborn glob re

