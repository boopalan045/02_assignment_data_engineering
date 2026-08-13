# Model Card — House Price Predictor

## Model Overview

| Field | Value |
|---|---|
| Model name | House Price Predictor |
| Version | 1.0 |
| Owner | PropSmart Realty Ltd. — Data Science |
| Date registered | 2026-08-13 01:26:36 UTC |
| Model type | Supervised regression |
| Algorithm | Random Forest Regressor (scikit-learn) |
| Hyperparameters | n_estimators=100, random_state=42 |
| Artifact | s3://houselab-lab01-381492281291-us-east-1/houselab/lab01/model/model.tar.gz |

## Intended Use

**Primary use case.** Generate instant indicative sale price estimates for residential
properties on the PropSmart customer portal, reducing enquiry-to-quote time from
approximately 3 days to under 30 seconds.

**Intended users.** Portal visitors (buyers and sellers) and internal estate agents
performing preliminary comparable analysis.

**Out of scope.** This model must NOT be used as the sole basis for mortgage lending
decisions, formal valuations, insurance underwriting, or tax assessment. It produces an
indicative estimate, not a certified appraisal.

## Training Data

| Field | Value |
|---|---|
| Source | Synthetic dataset generated with NumPy (seed=42) |
| Records | 500 |
| Train / validation split | 80% (400 rows) / 20% (100 rows) |
| Catalogued at | s3://houselab-lab01-381492281291-us-east-1/houselab/lab01/raw/houses.csv |
| Feature Group | house-price-fg-lab01 |

### Features

| Feature | Type | Range | Description |
|---|---|---|---|
| size_sqft | integer | 600 – 2999 | Floor area in square feet |
| bedrooms | integer | 1 – 5 | Number of bedrooms |
| age_years | integer | 0 – 49 | Age of the property in years |
| distance_km | float | 1.0 – 30.0 | Distance from city centre |
| has_garage | binary | 0 or 1 | Garage present |

**Target:** `price_usd` — sale price in USD.

## Performance

| Metric | Value |
|---|---|
| MAE (validation) | $26,019 |
| R-squared (validation) | 0.9131 |
| MAE as % of mean price | 12.9% |

### Feature Importance

| Rank | Feature | Importance |
|---|---|---|
| 1 | size_sqft | 0.6387 |
| 2 | distance_km | 0.2730 |
| 3 | age_years | 0.0433 |
| 4 | bedrooms | 0.0364 |
| 5 | has_garage | 0.0087 |

Size and distance from the city centre dominate, consistent with the known data
generating process and with real-estate domain intuition.

## Limitations and Ethical Considerations

- **Synthetic training data.** The model has never seen a real transaction. Performance
  on production data is unknown and must be re-validated before any deployment.
- **Left censoring.** 83 of 500 records (17%) were
  clipped at the $50,000 floor. Estimates for low-value properties are unreliable.
- **No location feature beyond distance.** Neighbourhood, school catchment, and
  amenities are unmodelled, and these are among the strongest real price drivers.
- **Extrapolation.** Tree ensembles cannot extrapolate. Properties outside the trained
  ranges (e.g. above 2,999 sqft) will receive predictions capped at the boundary values.
- **Fairness.** No protected or proxy attributes are used. Should postcode-level features
  be added later, they must be audited for correlation with protected characteristics,
  since location data can encode historical discrimination.
- **No drift monitoring.** Out of scope for this lab. Production deployment requires
  monitoring before go-live.

## Approval

Registered to Model Package Group `house-price-predictor-lab01` with status
**PendingManualApproval**. A human reviewer must approve this version before it can be
promoted. No automatic path to production exists.
