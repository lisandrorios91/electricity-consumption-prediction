# Predicting Monthly Electricity Consumption

Predicting monthly grid-electricity use for about 1,650 City of Toronto facilities from ENERGY STAR Portfolio Manager data. The project covers data cleaning, exploratory analysis, feature engineering, and regression models (Ridge and Random Forest).

**Tools:** Python · pandas · NumPy · Matplotlib · seaborn · scikit-learn

> **Update:** the first version reported R² ≈ 0.99, but that result came from **data leakage**: several features were calculated from the target. The modelling was rebuilt with leak-free features and a time-based train/test split. See the [pull request](../../pulls?q=is%3Apr) for the full before/after.

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
4. **Feature engineering.** Season flags, property type, and **lag features** (usage 1 and 2 months back, 3-month rolling average) that only look at past months.
5. **Modelling.** A naive "same as last month" baseline, Ridge regression, a Random Forest tuned with `TimeSeriesSplit`, and a Random Forest that predicts the % change from last month. Trained on Jan–Aug 2022, tested on Sep–Nov 2022.

## Results

Test period: September–November 2022 (4,933 property-months not seen during training).

| Approach | MAE (kWh) | R² |
|---|---|---|
| Original version (data leakage) | 4,349 | 0.99 ❌ not valid |
| Random Forest, building + season only | 54,613 | 0.79 |
| Random Forest, with usage history | 10,405 | 0.97 |
| Random Forest, predicting % change | 8,582 | 0.988 |
| **Baseline: "same as last month"** | **8,156** | **0.989** |

**Key findings**

- **Past usage is the strongest predictor by far.** Last month's usage accounts for over 80% of the Random Forest's feature importance. Without usage history, R² drops from 0.97 to 0.79.
- **No model beat the naive baseline.** These facilities use electricity very consistently from month to month, and with only 11 months of data the models can't learn yearly seasonality.
- **What would help:** two or more years of history ("same month last year"), weather data (heating and cooling degree-days), and separate handling for the few very large facilities that cause most of the error.

## How to run

Put the four CSV files in the same folder as the notebook, then run:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
jupyter notebook electricity_consumption.ipynb
```

## Team

Group project, Master of Data Analytics, University of Niagara Falls Canada.

Maria Alejandra Boada Rodriguez · Yovanni Rojas Cardona · Daniel Olmedo Zapata Gaibor · **Lisandro Rios**
