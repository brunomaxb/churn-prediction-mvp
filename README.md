Churn Analysis (Online Retail II) — EDA & Feature Engineering

This project delivers an Exploratory Data Analysis (EDA) and feature engineering pipeline for churn propensity using the Online Retail II dataset (UCI / Kaggle).
The scope intentionally stops before model training; the goal is to produce a clean, well-documented dataset and clear business insights for downstream modeling.

Scope: EDA, cleaning, RFM feature construction, churn label definition, class imbalance analysis, and train–test preparation (with robust scaling).
Out of scope: model training & evaluation (can be added later).

1) Problem Statement

Given historical B2B transactions from an online retail business, estimate the probability that a customer will churn, defined as no purchases in the last 6 months of the dataset among engaged customers (customers with ≥ 3 purchases in the analysis window).
The objective is to prepare a high-quality dataset and actionable insights for a future predictive model and retention strategy.

2) Dataset

Source: Online Retail II (UCI/Kaggle) https://chatgpt.com/c/68901828-10ec-832e-abc5-fd8f2f6c406d#:~:text=1)%20Problem%20Statement,01%2D01).
Format used here: consolidated CSV for reproducibility. The original dataset is an Excel file with multiple sheets; in this project we filter by date to keep 2010–2011.
Raw columns (typical): InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice/Price, CustomerID/Customer ID, Country.
Although the original is a multi-sheet XLSX, this project uses a single CSV and filters by date (2010-01-01 ≤ InvoiceDate < 2012-01-01).

3) Project Structure
|── MVP_Churn_OnlineRetailII.ipynb     # Main exploratory notebook (EDA)
├── data/
│   └── online_retail_II.csv               # Consolidated CSV (ignored if large)
├── README.md
└── requirements.txt

4) Environment & Reproducibility

Create a fresh environment and install dependencies:
pip install -r requirements.txt
If you use Google Colab, the notebook cells may install/check versions as needed.

5) Pipeline Summary
5.1 Data Loading
Load CSV and parse InvoiceDate.
Filter analysis window to 2010–2011.

5.2 Cleaning & Standardization
Keep only valid purchases (Quantity > 0, Price/UnitPrice > 0).
Remove rows without Customer ID.
Replace missing Description with "Unknown".
Winsorize Price at the 99th percentile to reduce extreme outliers.
Create Revenue = Quantity × Price/UnitPrice.

5.3 Target Definition (Churn)
Engaged customers: customers with ≥ 3 purchases (counted by InvoiceNo when available).
Churn label: 1 if > 180 days since last purchase (relative to dataset max date), else 0.

5.4 RFM Feature Engineering
Recency: days since last purchase (relative to dataset max date).
Frequency: number of invoices (nunique(InvoiceNo); fallback to row count).
Monetary: sum of Revenue per customer.

5.5 Unified Dataset for Modeling
Merge RFM + churn (+ Country), then apply one-hot encoding to Country (pd.get_dummies(..., drop_first=True)).
Train–test split with stratify=y to preserve class proportions.
Scale continuous features (Recency, Frequency, Monetary) with RobustScaler (median & IQR → robust to outliers).

6) EDA Highlights
Distributions: Price and Quantity are strongly right-skewed; Price benefits from log-scale visualization.
RFM × Churn: churned customers show higher Recency, lower Frequency, and lower Monetary than active customers.
Correlations: moderate positive correlation between Frequency and Monetary; weak negative correlations with Recency.
Class Imbalance: even among engaged customers, churn vs active shows moderate imbalance (e.g., ~39% churn in one run).

7) Business Insight (Actionable)
A practical retention system can prioritize customers who:
have > 90 days without purchases,
show historically low volume/value,
present a decreasing purchase frequency.
Trigger email campaigns, personalized offers, and sales-rep follow-ups to reduce churn and increase Customer Lifetime Value (CLV).

8) How to Run
Place data/online_retail_II.csv locally (or point to your path).
Open notebooks/MVP_Churn_OnlineRetailII.ipynb.
Run cells in order:
Blocks 1–7: loading and cleaning
Blocks 8–14: EDA (histograms, KDE, RFM visualizations, correlations)
Blocks 15–17: missing values, winsorization, feature consolidation
Blocks 18–19: train–test split and scaling; class balance checks

9) Next Steps (Suggested)
Baseline models: Logistic Regression (with class_weight='balanced') and tree ensembles (Random Forest, XGBoost).
Resampling: Try SMOTE on the training set; compare with class weights.
Evaluation: Prefer ROC-AUC, PR-AUC, Recall for churn (minority class), and F1-score.
Calibration: Calibrate probabilities (Platt/Isotonic) for operational thresholds.
Feature enrichment: trends for recency/frequency, seasonality flags, RFM buckets, cohort indices.

10) Notes & Assumptions
Returns (Quantity < 0) and invalid prices are removed.
Monetary is recomputed after winsorization to keep consistency.
The project uses CSV for reproducibility even though the original dataset is XLSX.
Churn definition restricted to engaged customers to avoid inflating churn with one-time buyers.

11) Citation
If you use this dataset, please cite the original UCI Online Retail II / Kaggle source as indicated on the Kaggle page.

12) License
This repository is intended for educational and research purposes. Check the dataset’s license on Kaggle/UCI for redistribution terms.
