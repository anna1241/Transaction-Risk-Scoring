# Transaction Risk Scoring

## Overview

This project develops a baseline machine learning system for identifying potentially fraudulent credit card transactions.

The project focuses on:

* Data cleaning and quality checks
* Exploratory data analysis
* Feature engineering
* SQL-based transaction analysis
* Imbalanced classification
* Machine learning model comparison
* Feature importance and SHAP analysis
* Transaction risk scoring

The goal is to demonstrate an end-to-end workflow for analyzing transaction data and building a model that can assign a risk score to individual transactions.

## Dataset

The project uses the **Credit Card Fraud Detection** dataset containing transactions made by European cardholders in September 2013.

The dataset contains:

* **284,807 transactions**
* **492 fraudulent transactions**
* **31 columns**
* **0.1727% fraudulent transactions**
* `Time`: Seconds elapsed between each transaction and the first transaction
* `Amount`: Transaction amount
* `V1` to `V28`: PCA-transformed numerical features
* `Class`: Target variable where `0` represents a legitimate transaction and `1` represents fraud

The dataset is highly imbalanced, with fraudulent transactions representing only a very small proportion of the total data. Therefore, metrics such as Precision, Recall, F1-score, ROC-AUC, and especially AUPRC are used instead of relying on accuracy alone.

## Data Quality Analysis

Initial analysis was performed to understand the structure and quality of the dataset.

The dataset contains **284,807 rows and 31 columns**. All columns contain complete values with no missing values.

The analysis also identified **1,081 duplicate rows**, which were investigated as part of the data quality checks.

## Exploratory Data Analysis

The analysis explored the relationship between fraudulent and legitimate transactions.

### Class Distribution

The target variable is highly imbalanced:

* Legitimate transactions: **284,315**
* Fraudulent transactions: **492**
* Fraud rate: **0.1727%**

This imbalance is an important consideration when evaluating fraud detection models.

### Transaction Amount

Transaction amounts were analyzed separately for legitimate and fraudulent transactions using descriptive statistics and visualizations.

### Transaction Time

The `Time` variable was transformed into:

* `Time_hours`
* `Time_days`
* `Hour`

Fraud rates were then analyzed across different hours to identify possible time-related patterns.

## Feature Engineering

Additional features were created to improve the representation of transaction behavior.

### Time-Based Features

* `Time_hours`
* `Time_days`
* `Hour`
* `Night`

The `Night` feature identifies transactions occurring before 6 AM or from 10 PM onward.

### Amount-Based Features

* `Amount_log`
* `Amount_to_Median`
* `High_Amount`

`Amount_log` applies a logarithmic transformation to transaction amounts.

`Amount_to_Median` measures the transaction amount relative to the overall median transaction amount.

`High_Amount` identifies transactions above the 95th percentile of transaction amounts.

### PCA-Based Features

The anonymized PCA variables (`V1` to `V28`) were also summarized into:

* `PCA_Magnitude`
* `PCA_Mean`
* `PCA_Std`
* `PCA_Max`
* `PCA_Min`

These features provide additional aggregated representations of the transformed transaction variables.

## SQL Analysis

SQLite was used to perform additional transaction-level analysis.

The SQL analysis included:

* Transaction counts by class
* Average transaction amount by class
* Maximum transaction amount by class
* Fraud transaction counts by hour
* Fraud rate by hour

This demonstrates the use of SQL alongside Python for exploratory and analytical tasks.

## Machine Learning

The dataset was divided into training and testing sets using an **80/20 stratified split**.

The final dataset contained:

* Training set: **227,845 transactions**
* Testing set: **56,962 transactions**

Stratification was used to maintain a similar fraud proportion in both datasets.

Three baseline classification models were evaluated:

### 1. Logistic Regression

Logistic Regression was implemented using a pipeline with:

* StandardScaler
* Logistic Regression
* `class_weight="balanced"`

### 2. Random Forest

The Random Forest model used:

* 200 trees
* Maximum depth of 12
* Balanced class weights
* Random state of 42

### 3. Gradient Boosting

The Gradient Boosting model used:

* 200 estimators
* Learning rate of 0.05
* Maximum depth of 3
* Random state of 42

## Model Evaluation

The models were evaluated using:

* Precision
* Recall
* F1-score
* ROC-AUC
* AUPRC
* Precision-Recall curves
* Confusion matrix

AUPRC was given particular attention because of the severe class imbalance in the fraud dataset.

### Results

| Model               | Precision | Recall |     F1 | ROC-AUC |  AUPRC |
| ------------------- | --------: | -----: | -----: | ------: | -----: |
| Logistic Regression |    0.0560 | 0.9184 | 0.1056 |  0.9736 | 0.7284 |
| Random Forest       |    0.8182 | 0.8265 | 0.8223 |  0.9763 | 0.8147 |
| Gradient Boosting   |    0.7727 | 0.8673 | 0.8173 |  0.9670 | 0.6741 |

The results show that the three models produced different trade-offs between precision and recall. The Random Forest model achieved an AUPRC of **0.8147** and was subsequently used for the feature importance, SHAP, and risk-scoring analysis.

## Precision-Recall Analysis

Precision-Recall curves were generated for all three models to examine their performance under the highly imbalanced fraud classification problem.

This provides a more useful view of fraud detection performance than accuracy alone because the positive fraud class is extremely rare.

## Feature Importance

Feature importance was calculated using the Random Forest model.

The analysis generated importance scores for the 40 features used by the model and visualized the top features contributing to transaction risk predictions.

## SHAP Analysis

SHAP was used to provide an additional interpretability layer for the Random Forest model.

A TreeExplainer was used to calculate SHAP values, followed by a SHAP summary plot to examine how the model features contribute to predictions.

This helps provide a better understanding of which transaction characteristics influence the model's fraud predictions.

## Transaction Risk Scoring

The Random Forest fraud probabilities were converted into a 0–100 risk score:

```text
Risk Score = Fraud Probability × 100
```

The scores were grouped into three demonstration categories:

* **0–30:** Low Risk
* **30–70:** Medium Risk
* **70–100:** High Risk

A high-risk transaction table was also generated by filtering transactions classified as High Risk and sorting them by risk score.

These thresholds are intended for demonstration and analysis rather than production fraud detection.

## Key Skills Demonstrated

* Python
* Pandas
* NumPy
* Scikit-Learn
* SQL
* SQLite
* Exploratory Data Analysis
* Data Cleaning
* Feature Engineering
* Imbalanced Classification
* Logistic Regression
* Random Forest
* Gradient Boosting
* Model Evaluation
* Precision-Recall Analysis
* Feature Importance
* SHAP
* Risk Scoring
* Data Visualization

## Limitations

This project is a baseline analytical and machine learning exercise.

The dataset contains anonymized PCA features and does not provide detailed business information such as customer identity, merchant information, transaction category, location, or payment method.

The risk score thresholds used in this project are also demonstration thresholds and would require further validation before being used in a real-world fraud detection system.

## Conclusion

This project demonstrates an end-to-end transaction risk scoring workflow, starting from data quality analysis and exploratory analysis through feature engineering, SQL analysis, machine learning, model evaluation, interpretability, and risk scoring.

The project highlights the importance of appropriate evaluation metrics when working with highly imbalanced fraud detection datasets and demonstrates how machine learning predictions can be converted into interpretable transaction risk scores.
