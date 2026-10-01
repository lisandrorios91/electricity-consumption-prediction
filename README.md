# Predicting Monthly Electricity Consumption

Predicting monthly grid-electricity use for about 1,650 City of Toronto facilities from ENERGY STAR Portfolio Manager data. The project covers data cleaning, exploratory analysis, feature engineering, and regression models (Ridge and Random Forest).

**Tools:** Python · pandas · NumPy · Matplotlib · seaborn · scikit-learn

> ⚠️ **Known issue (fix in progress):** The current model results (R² ≈ 0.99) are inflated by **data leakage**. Several engineered features, `kWh_per_sqft`, `usage_per_occupancy` and the per-property usage statistics, are calculated from the target variable itself. A corrected version with leak-free features and a time-based train/test split is coming in a pull request.

## Business problem

Facility managers need reliable monthly electricity forecasts for budgeting, energy-efficiency planning and spotting buildings that use more energy than they should. The goal is to predict each property's monthly kWh usage from its characteristics and consumption history.

## Data

Four CSV exports from ENERGY STAR Portfolio Manager covering City of Toronto facilities (public service buildings, transit stations, parking, fire stations, libraries and more):

| File | Rows | Contents |
|---|---|---|
| `meter_entries.csv` | 26,222 | Monthly meter readings: usage, units, cost |
| `meters.csv` | 2,585 | Meter type and units per property |
| `properties.csv` | 1,760 | Property type, gross floor area, occupancy |
| `uses.csv` | 1,760 | Floor area by use type |

After filtering to **Electric – Grid (kWh)** meters, the modelling dataset has **18,109 property-month records** covering January–November 2022.

## What's in the notebook

1. **Cleaning.** Converted text placeholders ("Not Available", "N/A") to missing values, fixed data types, removed 91 negative usage readings, and filtered to grid-electricity meters.
2. **Merging.** Joined meter readings to property characteristics.
3. **Exploratory analysis.** Usage distribution and outliers, usage vs. floor area, usage by property type, correlations, and seasonal patterns.
4. **Feature engineering.** Season flags, intensity features, per-property statistics, one-hot encoded property type, and standard scaling.
5. **Modelling.** Ridge regression as a baseline and a Random Forest tuned with GridSearchCV.

## How to run

Put the four CSV files in the same folder as the notebook, then run:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
jupyter notebook electricity_consumption.ipynb
```

## Team

Group project, Master of Data Analytics, University of Niagara Falls Canada.

Maria Alejandra Boada Rodriguez · Yovanni Rojas Cardona · Daniel Olmedo Zapata Gaibor · **Lisandro Rios**
