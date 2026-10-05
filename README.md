Sure — here’s a clean README you can use directly for the repo.

```markdown
# Freight Rate Prediction

This project predicts freight **posted rates** using machine learning based on shipment characteristics such as distance, weight, equipment type, market conditions, date information, and geographic pickup/delivery regions.

The project includes data cleaning, exploratory analysis, feature engineering, model comparison, CatBoost tuning, validation prediction generation, and the required December scenario analysis.

## Project Objective

The objective is to build a regression model that predicts `posted_rate` for freight loads.

The final model is then used to generate predictions for the unlabeled validation dataset and produce:

```text
validation_predictions.csv
```

with exactly:

```text
load_id,predicted_rate
```

## Dataset

The development dataset contains shipment information including:

- Pickup and delivery locations
- Pickup and delivery coordinates
- Distance
- Equipment type
- Weight
- Date
- Market index
- Quote signal
- Posted rate

The provided validation dataset contains the same shipment features but does not contain `posted_rate`.

## Data Cleaning

The main data-quality issues identified were:

- Missing values in `weight`
- Missing values in `market_index`
- Negative weight values
- A small number of unusually high `posted_rate` observations

Invalid negative weights were removed.

Statistical outliers in the target were investigated using the IQR method. A small number of extreme upper-rate observations were removed during the final modeling iteration.

## Feature Engineering

The following additional features were created:

- Day of week
- Month
- Weekend indicator
- Month-end indicator
- Cyclical day-of-week features
- Cyclical month features
- Pickup geographic region
- Delivery geographic region

Geographic regions were generated using **K-Means clustering** on pickup and delivery latitude/longitude coordinates.

The model uses the following main features:

```text
distance
weight
market_index
quote_signal
equipment
pickup_region
delivery_region
is_weekend
is_month_end
dow_sin
dow_cos
month_sin
month_cos
```

## Validation Strategy

The labeled dataset was sorted chronologically and split into:

- 70% training
- 15% validation
- 15% test

A chronological split was used instead of a random split because freight rates may change over time.

The final test set was kept separate from model selection.

## Models Tested

Several regression models were evaluated:

- Ridge Regression
- Random Forest Regressor
- HistGradientBoostingRegressor
- XGBoost
- Multi-Layer Perceptron
- CatBoost

The models were compared using:

- MAE
- RMSE
- R²
- MAPE

## Final Model

CatBoost produced the strongest overall validation performance and was selected as the final model.

Selected configuration:

```text
iterations = 800
learning_rate = 0.03
depth = 6
l2_leaf_reg = 3
loss_function = RMSE
```

Final held-out test performance:

```text
MAE   = $105.38
RMSE  = $248.34
R²    = 0.9670
MAPE  = 6.41%
```

Feature importance analysis showed that **distance** was the dominant predictor, followed by equipment type, weight, geographic region, and market-related features.

## Validation Predictions

The trained model is applied to all rows in:

```text
validation.csv
```

The final output is saved as:

```text
validation_predictions.csv
```

with exactly:

```text
load_id,predicted_rate
```

## December Scenario

The project also uses the provided fixed December scenario:

```text
Lexington → Fort Wayne
Distance: 360 miles
Equipment: Dry Van
Weight: 32,000 lb
Dates: 2025-12-01 to 2025-12-31
```

The model predicts the freight rate for each day in December.

The completed file is saved as:

```text
december_predictions.csv
```

The provided `score.py` script validates both output files and generates:

```text
scorer_results/candidate_december.png
```

## Repository Structure

```text
.
├── training.ipynb
├── validation.ipynb
├── december.ipynb
├── score.py
├── requirements.txt
├── README.md
├── validation_predictions.csv
├── december_predictions.csv
├── freight_rate_model.pkl
└── geographic_kmeans.pkl
```

## Installation

Create a virtual environment if needed:

```bash
python -m venv venv
```

Activate it on Linux/macOS:

```bash
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## Run the Project

Run the notebooks in this order:

```text
1. training.ipynb
2. validation.ipynb
3. december.ipynb
```

Then run the scorer:

```bash
python3 score.py \
  --predictions validation_predictions.csv \
  --december-predictions december_predictions.csv
```

If successful, the scorer will validate the files and generate the December chart.

## Main Dependencies

```text
pandas
numpy
matplotlib
scikit-learn
xgboost
catboost
joblib
```


