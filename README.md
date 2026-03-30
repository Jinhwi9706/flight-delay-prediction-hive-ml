# ✈️ Flight Delay Prediction — Hive + Machine Learning

Predicting U.S. domestic flight delays using large-scale data processed on Apache Hive (AWS EMR) and machine learning models in Python.

---

## Overview

This is a team project where each member handled one year of U.S. domestic flight data (2001, 2003, 2004, 2008) sourced from the [Harvard Dataverse](https://dataverse.harvard.edu/).

**My responsibilities:**
- Set up Hive environment on AWS EMR (EC2)
- Loaded 2004 flight data (~7M rows) into HDFS
- Wrote and executed 3 analytical HiveQL queries
- Exported query results as CSV for downstream Python analysis
- Built and evaluated machine learning models independently

Each member exported their year's data as a 30,000-row sample CSV. The four CSVs were merged (120,000 rows total) for model training.

---

## Tech Stack

| Layer | Tools |
|---|---|
| Big Data Processing | Apache Hive, Hadoop (YARN), AWS EMR (EC2) |
| Data Analysis | Python, pandas, NumPy |
| Machine Learning | scikit-learn, XGBoost |
| Visualization | matplotlib, seaborn |

---

## Repository Structure

```
flight-delay-prediction-hive-ml/
│
├── hive_queries_only.pdf     # HiveQL queries with execution results (2004 data)
├── flight_delay_prediction.ipynb  # Python analysis: preprocessing + ML models
│
├── 2004_sample.csv           # 2004 flight data exported from Hive (my data)
├── 2001_sample.csv           # 2001 flight data (team member)
├── 2003_sample.csv           # 2003 flight data (team member)
├── 2008_sample.csv           # 2008 flight data (team member)
└── Target Data Set.xlsx      # Unlabeled target data for final prediction
```

---

## Hive Analysis (2004 Data)

All queries were run on AWS EMR using HiveQL against ~7M rows of 2004 U.S. flight records.

**Query 1 — Top 3 Airports by Total Delay Time**
- Joined flight data with airport reference table
- Aggregated arrival + departure delays (converted to hours)
- Result: Chicago O'Hare (162,691 hrs) > Atlanta Hartsfield (151,524 hrs) > Dallas-Fort Worth (84,540 hrs)

**Query 2 — Top 3 Airlines by Total Delay Time**
- Joined flight data with carrier reference table
- Result: Southwest Airlines (268,284 hrs) > American Airlines (196,178 hrs) > Delta Air Lines (179,034 hrs)

**Query 3 — Arrival vs Departure Delay Comparison**
- Aggregated total arrival and departure delays for 2004
- Result: Departure delay (920,969 hrs) > Arrival delay (757,943 hrs)

---

## Python Analysis

### Data Preprocessing
- Merged 4-year CSV samples → 120,000 rows
- Replaced Hive null values (`\N`) with NaN
- Dropped `CancellationCode` (97% missing)
- Applied one-hot encoding for carrier, origin, and destination
- Imputed missing values: mean for numeric columns, 0 for near-zero-variance columns

### Models & Results

| Model | Accuracy | Precision |
|---|---|---|
| Logistic Regression (one-hot features) | 93.3% | 94.2% |
| Logistic Regression (numeric only) | 71.8% | 74.7% |
| Random Forest | 81.5% | 86.6% |
| **XGBoost** | **90.2%** | **95.0%** |

**XGBoost was selected as the final model.**
Although Logistic Regression (one-hot) showed higher accuracy on the test set, it relied on 612 one-hot encoded features, raising overfitting concerns. XGBoost achieved strong performance with a compact numeric feature set, making it more reliable for generalization.

---

## Key Findings

- Departure delays consistently exceed arrival delays — suggesting downstream propagation effects
- Chicago O'Hare and Atlanta Hartsfield are the highest-risk airports for delay accumulation
- Southwest Airlines had the highest total delay volume, likely due to its large fleet size rather than operational inefficiency

---

## Data Source

[U.S. Airline On-Time Performance Data — Harvard Dataverse](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/HG7NV7)
