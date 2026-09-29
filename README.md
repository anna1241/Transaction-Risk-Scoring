# Transaction Risk Scoring

## Overview

This project develops a baseline machine learning system for identifying potentially fraudulent credit card transactions.

The project focuses on data cleaning, exploratory data analysis, feature engineering, SQL-based analysis, imbalanced classification, model comparison, and model interpretability. Three baseline machine learning models were evaluated: Logistic Regression, Random Forest, and Gradient Boosting.

The final system converts predicted fraud probabilities into transaction-level risk scores that can be categorized into Low, Medium, and High risk.

---

## Business Problem

Credit card fraud detection is a highly imbalanced classification problem because fraudulent transactions represent only a very small proportion of all transactions.

The objective of this project is to develop a model that can distinguish potentially fraudulent transactions from legitimate ones while using appropriate evaluation metrics for imbalanced data.

The project focuses on:

* Identifying patterns associated with fraudulent transactions
* Engineering useful transaction-level features
* Comparing multiple baseline classification models
* Evaluating models using appropriate fraud detection metrics
* Understanding the features influencing model predictions
* Generating interpretable transaction risk scores

---

## Dataset

The project uses the **Credit Card Fraud Detection** dataset.

The dataset contains transactions made by European cardholders during September 2013. It contains **284,807 transactions**, including **492 fraudulent transactions**.

The dataset is highly imbalanced, with fraudulent transactions representing approximately **0.172%** of all observations.

### Features

The dataset contains:

* `Time` — seconds elapsed between each transaction and the first transaction
* `Amount` — transaction amount
* `V1` to `V28` — principal components obtained through PCA transformation
* `Class` — target variable

  * `0` = legitimate transaction
  * `1` = fraudulent transaction

Due to confidentiality, the original transaction features are not provided. The majority of the variables have therefore been transformed using PCA.

### Dataset Source

The dataset was collected and analyzed through a research collaboration between Worldline and the Machine Learning Group of Université Libre de Bruxelles (ULB).

---

## Project Workflow

```text
Raw Transaction Data
        ↓
Data Cleaning & Quality Checks
        ↓
Exploratory Data Analysis
        ↓
Feature Engineering
        ↓
SQL Analysis
        ↓
Train/Test Split
        ↓
Baseline Machine Learning Models
        ↓
Model Evaluation
        ↓
Feature Importance
        ↓
SHAP Interpretability
        ↓
Transaction Risk Scoring
```

---

## Data Cleaning

The dataset was inspected for:

* Missing values
* Duplicate records
* Data types
* Class distribution
* Numerical feature statistics
* Potential outliers

The target variable was also analyzed to understand the severe class imbalance between legitimate and fraudulent transactions.

---

## Exploratory Data Analysis

Exploratory analysis was performed to understand transaction behavior and identify patterns associated with fraud.

Analysis included:

* Fraud vs. legitimate transaction distribution
* Transaction amount distribution
* Transaction amount by fraud class
* Transaction activity over time
* Fraud rate by transaction hour
* Distribution of PCA-derived variables

Visualizations were created using Matplotlib and Seaborn.

---

## Feature Engineering

Because the dataset does not contain customer IDs, merchant information, or original transaction categories, customer-level features such as customer frequency and customer recency were not created.

Instead, transaction-level features were engineered from the available data.

### Engineered Features

* `Time_hours` — transaction time converted from seconds to hours
* `Time_days` — transaction time converted from seconds to days
* `Hour` — approximate hour within the transaction period
* `Night` — indicator for transactions occurring during nighttime
* `Amount_log` — logarithmic transformation of transaction amount
* `Amount_to_Median` — transaction amount relative to the dataset median
* `High_Amount` — indicator for transactions above the 95th percentile
* `PCA_Magnitude` — combined magnitude of the PCA features
* `PCA_Mean` — mean of PCA-transformed features
* `PCA_Std` — standard deviation of PCA-transformed features
* `PCA_Max` — maximum PCA feature value
* `PCA_Min` — minimum PCA feature value

These features were used alongside the original PCA variables for model development.

---

## SQL Analysis

SQLite was used to demonstrate SQL-based transaction analysis.

SQL queries were used to examine:

* Transaction counts by fraud class
* Average transaction amounts
* Maximum transaction amounts
* Transaction activity by hour
* Fraud rates by transaction hour

Example analysis:

```sql
SELECT
    Class,
    COUNT(*) AS transaction_count,
    AVG(Amount) AS average_amount,
    MAX(Amount) AS maximum_amount
FROM transactions
GROUP BY Class;
```

---

## Machine Learning Models

Three baseline classification models were developed and compared.

### 1. Logistic Regression

Logistic Regression was used as a simple and interpretable baseline model.

Standardization was applied to the input features, and class weighting was used to account for the imbalanced target.

### 2. Random Forest

Random Forest was used as a tree-based ensemble model capable of capturing nonlinear relationships between transaction features and fraud risk.

Class weighting was used to help address the class imbalance.

### 3. Gradient Boosting

Gradient Boosting was evaluated as another tree-based ensemble approach for identifying nonlinear patterns in the transaction data.

---

## Model Evaluation

Due to the severe class imbalance, accuracy was not treated as the primary evaluation metric.

The models were evaluated using:

* Precision
* Recall
* F1-score
* ROC-AUC
* Average Precision / AUPRC
* Confusion Matrix
* Precision-Recall Curves

### Why AUPRC?

With only approximately 0.172% fraudulent transactions, a model could achieve very high accuracy simply by predicting most transactions as legitimate.

Precision-Recall analysis provides a more useful view of performance when the positive class is extremely rare.

---

## Model Comparison

The models were compared using a performance table containing:

| Model               | Precision | Recall | F1 | ROC-AUC | AUPRC |
| ------------------- | --------: | -----: | -: | ------: | ----: |
| Logistic Regression |         — |      — |  — |       — |     — |
| Random Forest       |         — |      — |  — |       — |     — |
| Gradient Boosting   |         — |      — |  — |       — |     — |

The values in this table are generated directly from the notebook after training and evaluating the models.

---

## Model Interpretability

### Feature Importance

Random Forest feature importance was used to identify which variables contributed most strongly to the model's predictions.

A feature-importance visualization was created to provide an overview of the most influential variables.

### SHAP Analysis

SHAP was used to provide more detailed model interpretability.

SHAP analysis helps explain:

* Which features influence predictions
* Whether feature values increase or decrease predicted fraud risk
* How individual transactions receive their predicted risk

This provides a more interpretable view of the machine learning model rather than treating it as a black box.

---

## Transaction Risk Scoring

The final model generates a probability representing the predicted likelihood of fraud.

The probability is converted into a risk score:

```text
Risk Score = Predicted Fraud Probability × 100
```

The score is then categorized into three risk levels:

```text
0–30     → Low Risk
30–70    → Medium Risk
70–100   → High Risk
```

These thresholds are used for demonstration purposes and are not intended to represent production fraud-management thresholds.

Example:

```text
Predicted Fraud Probability: 0.82
Risk Score: 82
Risk Level: High
```

---

## Project Structure

```text
transaction-risk-scoring/
│
├── data/
│   └── creditcard.csv
│
├── notebooks/
│   └── transaction_risk_scoring.ipynb
│
├── sql/
│   └── transaction_analysis.sql
│
├── outputs/
│   ├── figures/
│   ├── model_comparison.csv
│   └── high_risk_transactions.csv
│
├── README.md
└── requirements.txt
```

> **Note:** The original dataset is not included in this repository if its distribution is restricted. The notebook can be run after downloading the dataset from its original source and placing it in the `data/` directory.

---

## Technologies Used

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Seaborn
* SHAP
* SQLite
* SQL
* Jupyter Notebook

---

## Key Skills Demonstrated

* Data Cleaning
* Exploratory Data Analysis
* Feature Engineering
* Imbalanced Classification
* Machine Learning
* Model Evaluation
* SQL Analysis
* Feature Importance
* SHAP Explainability
* Risk Scoring
* Data Visualization

---

## Limitations

This project uses an anonymized historical dataset containing only two days of transactions.

The dataset does not contain:

* Customer identifiers
* Merchant information
* Geographic information
* Payment method
* Original transaction categories
* Calendar dates

Therefore, customer-level behavioral features and detailed business-level transaction analysis cannot be performed.

The models developed in this project are baseline experiments and should not be considered production-ready fraud detection systems.

