# House Price Predictor: AWS Machine Learning Basics Lab

BITS Pilani | Professional Certificate in AI/ML

This is the lab notebook for the House Price Predictor business problem statement. It builds a small end-to-end AWS ML pipeline: generate synthetic property data, land it in S3, catalogue it with Glue, query it with Athena, register the features in SageMaker Feature Store, train a Random Forest locally, and register the versioned model in SageMaker Model Registry behind a manual approval gate.

No SageMaker training or hosting jobs run in this notebook. Everything is serverless or API-only, so it works even on an account with ML compute quota set to zero.

## What's in this repo

| File | Purpose |
|---|---|
| `house_price_predictor_aws_lab_1.ipynb` | The lab notebook, 9 steps plus setup, verification, and cleanup |
| `model_artifacts/` | Local copies of the trained model (`model.joblib`, `model.tar.gz`) written by Step 7 |

## Prerequisites

- An AWS account with an IAM user that has permissions for S3, Glue, Athena, and SageMaker
- AWS CLI installed and configured (`aws configure`)
- Python 3.12
- Packages: `boto3`, `sagemaker` (pin to 2.x, e.g. `sagemaker==2.257.5` — the 3.x line restructures the API this notebook uses), `pandas`, `numpy`, `scikit-learn`, `joblib`

An `AmazonSageMaker-ExecutionRole` IAM role also needs to exist, with a trust policy that lets `sagemaker.amazonaws.com` assume it, and with S3 and SageMaker access attached. Cell 0 falls back to `role/AmazonSageMaker-ExecutionRole` if it can't auto-detect a role.

## Running it

1. Activate the virtual environment and install the packages above
2. Open the notebook and select that environment as the kernel
3. Run the cells top to bottom — later cells depend on variables and AWS resources the earlier ones create
4. Re-running from the top is safe; the resource-creation cells already handle "already exists" errors

## What gets created in AWS

| Resource | Name |
|---|---|
| S3 bucket | `houselab-lab01-{account}-{region}` |
| Glue database / table | `houselab_db.houses_raw` |
| Feature Group | `house-price-fg-lab01` |
| Model Package Group | `house-price-predictor-lab01` |

The notebook's verification cell (near the end) checks all seven success criteria from the problem statement and prints a pass/fail summary.

## Results

Random Forest, 100 trees, trained on 500 synthetic records (80/20 split):

- MAE ≈ $26,000
- R² ≈ 0.91

Most of that error is irreducible noise baked into the synthetic data generation, not underfitting — see the "Interpreting the results" section in the notebook for the full explanation.

## Submission

Take your AWS console screenshots (S3 bucket, Glue table, Athena query, Feature Group, Model Registry entry) before running the Cleanup cell — deletion is irreversible and removes the evidence the success criteria ask for. The Cleanup cell is guarded by `RUN_CLEANUP = False`, so it won't delete anything unless you flip that flag and re-run it.
