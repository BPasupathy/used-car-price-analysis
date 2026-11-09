## Live Vercel deployment

[Open the deployed project](https://used-car-price-analysis-bpasupathy.vercel.app)

# Practical Application 2: What Drives the Price of a Car?

**Notebook:** [notebooks/prompt_II.ipynb](notebooks/prompt_II.ipynb)

## Executive Summary & Business Understanding

This project analyzes a supplied dataset of **426,880 used-vehicle listings** using the CRISP-DM framework. The objective is to identify vehicle attributes associated with listing prices and translate the findings into practical guidance for dealership acquisition and appraisal teams.

The source CSV is intentionally excluded from this public repository because it contains a large record-level dataset. Place the supplied file at `data/vehicles.csv` to run the notebook.

## Data & Method

- Source rows: **426,880**
- Cleaning filters: listing price $1,500–$80,000; model year 1995–2022; odometer 1,000–250,000 miles.
- Excluded: listing identifier, VIN, and `size` (approximately 71.8% missing).
- Modeling complete cases: **110,429 rows** (25.9% of source records).
- Predictors: vehicle age, odometer, manufacturer, condition, cylinders, fuel, title status, transmission, drivetrain, and body type.
- Model sample: up to 40,000 rows; 75/25 train/test split; five-fold cross-validation.
- Target: advertised/listing price, not confirmed transaction price.

## Key Findings

The following are **median listing prices in the cleaned complete-case sample**, not causal premiums:

| Attribute | Category | Median listing price |
|---|---:|---:|
| Body type | Pickup | $27,990 |
| Body type | Truck | $22,000 |
| Body type | Sedan | $7,995 |
| Drivetrain | 4WD | $17,495 |
| Drivetrain | RWD | $16,995 |
| Drivetrain | FWD | $7,900 |
| Fuel | Diesel | $28,989 |
| Fuel | Gas | $11,800 |
| Title status | Clean | $12,500 |
| Title status | Rebuilt | $11,000 |
| Title status | Salvage | $7,995 |
| Title status | Parts only | $4,000 |

Interpretation:
1. **Vehicle age and mileage matter.** The EDA shows lower asking prices at higher ages and odometer readings. Appraisals should use both measures rather than a single mileage threshold.
2. **Configuration is associated with price.** Pickups and trucks, 4WD/RWD listings, and diesel listings have higher sample medians than sedans, FWD, and gas listings. These groups also differ in age, use case, and market mix.
3. **Title status is a material risk-screening variable.** Salvage and parts-only listings have lower median asking prices than clean-title listings. Non-clean titles require additional inspection, legal/title verification, and reconditioning diligence.
4. **Do not interpret raw group medians as like-for-like premiums.** They are unadjusted and can be confounded by vehicle age, mileage, manufacturer, region, and condition.

## Model Comparison

Four models were evaluated using five-fold cross-validation for hyperparameter selection. The metrics below are from the fixed 40,000-row sample (random state 42) and held-out test split.

| Model | Test RMSE ($) | Test R² | Selected hyperparameters |
|---|---:|---:|---|
| Linear Regression | 5,946.00 | 0.786 | Baseline |
| Ridge Regression | 5,946.59 | 0.786 | `alpha=0.1` |
| Lasso Regression | 5,946.24 | 0.786 | `alpha=0.1` |
| Decision Tree Regressor | 4,900.88 | 0.855 | `max_depth=14`, `min_samples_split=20` |

The decision tree performed best on the held-out sample, consistent with nonlinear relationships in vehicle pricing. Linear and regularized models provide a simpler benchmark and coefficient-based interpretation. These scores are specific to the sample, filtering rules, and split; they are not guaranteed on future inventory.

## Actionable Dealer Recommendations

- **Auction screening:** Consider late-model pickups and trucks, and 4WD vehicles, when comparable local demand and acquisition costs support the asking-price signal.
- **Mileage guardrails:** Use age- and segment-specific mileage bands. Avoid a universal 120,000-mile cutoff; validate thresholds against local sales and reconditioning economics.
- **Title diligence:** Treat clean-title verification as a core acquisition control. Price non-clean-title vehicles only after title, safety, and repair risks are quantified.
- **Appraisal workflow:** Use the trained pipeline as a reference estimate alongside local comparable listings, inspection results, reconditioning costs, and expected days-to-sale.
- **Model governance:** Track realized sale-price error by region, body type, price band, and manufacturer before using predictions to set purchase limits.

## Limitations

- The dataset contains advertised prices, not verified sale prices.
- Complete-case analysis retains 110,429 of 426,880 rows and may introduce selection bias.
- The age feature uses 2022 as the source dataset baseline. Update it for any current-market deployment.
- Median comparisons are unadjusted; they should not be presented as causal effects.
- The model uses a random split rather than a time-based or geography-based validation design.

## Repository Structure

```text
used-car-price-analysis/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── .gitkeep
├── images/
│   ├── continuous_distributions.png
│   ├── categorical_comparisons.png
│   └── feature_importance.png
└── notebooks/
    └── prompt_II.ipynb
```

## Run Locally

```bash
python -m pip install -r requirements.txt
# Place the supplied CSV at data/vehicles.csv
jupyter notebook notebooks/prompt_II.ipynb
```
