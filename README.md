# Soil Moisture Prediction using SAR Data

This repository contains  machine learning pipeline to predict soil moisture from Sentinel-1 SAR backscatter and SMAP data.

## Project Overview

The goal of this project is to estimate surface soil moisture from:
- VV and VH polarization backscatter (Sentinel-1 SAR)
- SMAP morning overpass soil moisture product (`smap_am`)[page:1]

Key stages:
- Data ingestion from CSV and basic sanity checks
- Exploratory Data Analysis (EDA) with and without `ydata-profiling`
- Outlier detection and removal based on physical constraints
- Feature engineering and transformations to reduce skewness
- Training and evaluation of multiple regression models for soil moisture prediction[page:1]

## Dataset

The dataset after cleaning consists of:
- ~30,000 samples, 4–5 columns depending on feature set
- Features: `VV`, `VH`, `smap_am`, derived feature `VVDivVH`
- Target: `soil_moisture` in the range \([0, 1]\)[page:1]

Preprocessing steps:
- Removal of duplicate rows (13 duplicates)
- Removal of physically impossible soil moisture values (soil_moisture > 1, 22 rows)
- Filtering soil moisture to \([0, 1]\), final cleaned size ≈ 30,712 rows before further filtering[page:1]

## EDA and Preprocessing

Two styles of EDA are used:
- Automated EDA with `ydata-profiling` (HTML report `eda_report.html`)
- Manual EDA with:
  - `.info()`, `.describe()`, null checks, duplicate checks
  - Histograms for all variables
  - Correlation matrix and heatmap
  - Scatter plots of features vs. `soil_moisture`[page:1]

Outlier and distribution handling:
- Imposing physical bounds on `soil_moisture` \([0, 1]\)
- Log transformation of `VV`, `VH`, `smap_am` after shifting by column-wise minimum
- Additional transformations on `smap_am` (sqrt + Box–Cox) to reduce skew
- Skewness monitoring before and after transformations[page:1]

Feature engineering:
- Creation of `VVDivVH = VV / VH` as an additional feature
- Final feature set: `VV`, `VH`, `smap_am`, `VVDivVH`[page:1]

## Modeling

Train–test split:
- Features: `VV`, `VH`, `smap_am`, `VVDivVH`
- Target: `soil_moisture`
- Train/test ratio: 80/20
- Shapes: `X_train` (19884, 4), `X_test` (4971, 4)[page:1]

Models implemented:
- Baseline: `DummyRegressor` (mean predictor)
- Linear models:
  - `LinearRegression` (with `StandardScaler` in a pipeline)
  - `Ridge` regression with `alpha=1.0`
- Tree-based ensembles:
  - `RandomForestRegressor` (n_estimators=400, min_samples_leaf=2)
  - `HistGradientBoostingRegressor` (max_depth=6, learning_rate=0.05, max_iter=500)
- Neural-style:
  - `MLPRegressor` (hidden layers (64, 64), ReLU, Adam, max_iter=500) with scaling pipeline[page:1]

Evaluation metrics:
- Root Mean Squared Error (RMSE)
- Mean Absolute Error (MAE)
- Coefficient of determination (R²)[page:1]

Best model (on test set):
- `HistGradientBoostingRegressor`
  - RMSE ≈ 0.1199
  - MAE ≈ 0.1002
  - R² ≈ 0.093
- Other models (Random Forest, MLP, Linear, Ridge, Dummy) are also benchmarked and summarized in a results DataFrame.[page:1]


