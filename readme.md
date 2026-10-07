# Forecasting Geomagnetic Storms (Dst Index) from Solar Wind Data

**Module:** SPC4003 Coding Practices in AI Development, Queen Mary University of London (final project, 2025/26)

## Project Overview

Geomagnetic storms can disrupt satellites, GPS and power grids. The Dst (Disturbance Storm-Time) index measures how strong a storm is: the more negative the value, the stronger the storm.

This project predicts the hourly Dst index from solar wind measurements taken near Earth. It compares a single linear regression model with a clustering-based approach that first identifies different solar wind "regimes" and then fits a separate model for each one.

## Headline Results

| Model | Test RMSE |
|---|---|
| Linear regression (baseline) | 13.49 nT |
| GMM clustering + conditional regression (k = 9) | **12.67 nT** |

- The conditional model is **6.1% more accurate** than the baseline.
- Backward elimination ranked the features by importance: **solar wind speed (V) > Bz > Bx > density > By**. Speed and Bz alone get within 0.04 nT of the full model's error.

## Dataset

**NASA OMNI2** hourly solar wind and geomagnetic data, 1998–2020 (`Omni_98_20.csv`, supplied by the module).

| Column | Meaning | Unit |
|---|---|---|
| `Bx`, `By`, `Bz` | Components of the interplanetary magnetic field | nT |
| `density` | Solar wind particle density | particles/cm³ |
| `V` | Solar wind speed | km/s |
| `Dst` | Geomagnetic storm index (target) | nT |

The original data is available from NASA SPDF: https://spdf.gsfc.nasa.gov/pub/data/omni/low_res_omni/

**Note:** the CSV is not included in this repository. Place `Omni_98_20.csv` in the same folder as the notebook before running it.

## Method

1. **Cleaning.** OMNI2 marks missing readings with fill values (999.9, 9999, 99999). I removed every row containing one, which took the dataset from 289,296 to 246,486 hourly records.
2. **Exploration.** I used a pairplot and a correlation matrix to inspect the features. Density was heavily skewed (skewness 3.14), so I log-transformed it (skewness 0.03).
3. **Time-based split.** The model trains on data before 2010 (150,811 rows) and is tested on 2010 onwards (95,675 rows). This mirrors real forecasting: the model never sees the future during training.
4. **Baseline.** A linear regression on standardised features, with the scaler fitted on training data only to avoid leakage.
5. **Clustering.** I fitted Gaussian Mixture Models for k = 2 to 10 and used BIC to choose the best number of clusters (k = 9).
6. **Conditional regression.** I trained one linear regression per cluster, then routed each test sample to its own cluster's model.
7. **Feature ranking.** Backward elimination using t-statistics, then test RMSE plotted against the number of features used.

## Repository Structure

```
├── Rawal_FinalProject.ipynb   # Full analysis: cleaning, EDA, models, plots and answers
└── README.md
```

## How to Run

Install the dependencies:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn statsmodels jupyter
```

Then open the notebook and run all cells:

```bash
jupyter notebook Rawal_FinalProject.ipynb
```

The notebook also runs in Google Colab: upload the notebook and `Omni_98_20.csv` to the session, then run all cells.

## Tools

Python · pandas · NumPy · scikit-learn · statsmodels · Matplotlib · seaborn · Jupyter

## AI Use

As the module required, AI assistance is documented in every code cell of the notebook: what I asked, what I used and what I changed.

## Author

**Frayansh Rawal** — BSc Applied Artificial Intelligence, Queen Mary University of London
[GitHub](https://github.com/Frayansh211206)
