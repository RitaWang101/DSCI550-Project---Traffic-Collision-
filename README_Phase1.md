# Spatiotemporal Analysis and Severity Prediction of Traffic Collisions in Los Angeles
##Project Structure

```
project/
├── Traffic_Collision_Data.csv        # Raw dataset (not included in repo)
├── collisions_clean.csv              # Cleaned output from Phase 1
├── notebooks/
│   ├── phase1-data_cleaning.ipynb    # Data Cleaning & EDA (this phase)
│   ├── 2_spatial_analysis.ipynb      # KDE, DBSCAN, spatial risk score
│   ├── 3_feature_engineering.ipynb   # Temporal & demographic features
│   └── 4_model_classification.ipynb  # Random Forest classifier
└── README.md
```
---

## Requirements

Install dependencies before running any notebook:

```bash
pip install pandas numpy matplotlib seaborn
```

---

## Phase 1 — Data Cleaning & EDA

**Notebook:** `phase1-data_cleaning.ipynb`  
### Dataset

- **Source:** [LA Open Data Portal — Traffic Collision Data from 2010 to Present](https://data.lacity.org/Public-Safety/Traffic-Collision-Data-from-2010-to-Present/d5tf-ez2w/about_data)
- **Recorded by:** Los Angeles Police Department (LAPD)
- **Raw size:** ~620,000 records, 18 columns
- **Coverage:** City of Los Angeles, 2010 to present
To run this notebook, download the dataset from the link above, save it as `Traffic_Collision_Data.csv`, and place it in the same directory as the notebook.

---

### What This Notebook Does

#### 1. Load & Inspect
Loads the raw CSV and prints shape, column names, data types, and missing value counts to understand the data before any processing.

#### 2. Parse Datetime
Combines `Date Occurred` and `Time Occurred` into a single `datetime` column. `Time Occurred` is zero-padded to 4 digits (e.g. `830` → `0830`) so it parses correctly with the format `%m/%d/%Y %H%M`.

#### 3. Extract Coordinates
Parses the `Location` column (stored as `"(lat, lon)"` strings) into separate numeric `Latitude` and `Longitude` columns using regex extraction.

#### 4. Clean & Filter
Applies the following steps in order:

| Step | Action | Reason |
|------|--------|--------|
| 1 | Drop `Cross Street` column | Not used in analysis; 29K nulls |
| 2 | Drop rows where `MO Codes` is null | MO Codes is the target variable — rows without it can't be labeled |
| 3 | Fill `Victim Age` nulls with median | Preserves 88K rows instead of dropping them |
| 4 | Fill `Victim Sex` nulls with `'X'` | Represents unknown rather than discarding data |
| 5 | Drop any remaining null rows | Handles ~960 Premise Code/Description nulls |
| 6 | Remove zero coordinates | `(0, 0)` means location was never recorded |
| 7 | Filter to LA bounding box | Keeps lat 33.7–34.35, lon −118.7–−118.15 |
| 8 | Drop duplicate `DR Number` | Ensures each collision is counted once |

#### 5. Exploratory Data Analysis (EDA)
Four visualizations are produced:

- **Collisions by Area Name** — horizontal bar chart showing which LAPD areas have the most incidents
- **Yearly & Monthly Trends** — line charts showing collision frequency over time
- **Victim Age Distribution** — histogram of victim ages
- **Victim Sex Distribution** — bar chart of M / F / X breakdown
- **MO Code Severity Preview** — counts of records containing severity codes 3024 (severe injury) and 3027 (fatality)

#### 6. Save Output
Exports the cleaned dataframe to `collisions_clean.csv` for use in Phase 2 (Spatial Analysis).

---

### Key MO Codes Used

| MO Code | Meaning |
|---------|---------|
| `3024` | Severe Injury |
| `3027` | Fatality |

These codes are used in later phases to construct the multi-class severity target variable.

---

### Output

- `collisions_clean.csv` — cleaned dataset passed to Phase 2


