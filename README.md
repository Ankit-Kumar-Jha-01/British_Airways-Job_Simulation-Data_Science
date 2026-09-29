<div align="center">

# ✈️ British Airways — Data Science Job Simulation (Forage)

**Two-part project: lounge eligibility lookup analysis and a predictive model for customer booking behaviour**

![Python](https://img.shields.io/badge/Python-3.10-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Scikit--learn](https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)
![Forage](https://img.shields.io/badge/Forage-Job_Simulation-6A5ACD?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

</div>

---

## 📑 Table of Contents

1. [Overview](#-overview)
2. [Requirements](#-requirements)
3. [Project Structure](#-project-structure)
4. [Data](#-data)
5. [Installation](#-installation)
6. [Project Methodology](#-project-methodology)
7. [Visualization](#-visualization)
8. [Technology Used](#-technology-used)
9. [Future Improvements](#-future-improvements)

---

## 🔎 Overview

This is a two-task project completed as part of the **British Airways Data Science Job Simulation (Forage)**.

- **Task 1 — Lounge Eligibility Lookup:** Built a reusable lookup table grouping flights by haul type and time of day to estimate the percentage of passengers eligible for Tier 1 / Tier 2 / Tier 3 lounge access, using the Summer Schedule dataset.
- **Task 2 — Predicting Customer Buying Behaviour:** Built a Random Forest classifier to predict whether a customer will complete a booking, using engineered features, target/one-hot encoding, cross-validation, and threshold tuning.

| Task | Deliverable | Key Result |
|---|---|---|
| Task 1 | Lounge eligibility lookup table (Excel) | 8 flight groups (haul × time of day), Tier 3 eligibility ranging **10.3%–17.3%** |
| Task 2 | Booking-completion prediction model (Random Forest) | **ROC-AUC 0.7902**, Recall **68.18%** at threshold 0.20 |

---

## ⚙️ Requirements

**Task 1 (Excel):**
- Microsoft Excel / Google Sheets (or any `.xlsx`-compatible viewer)

**Task 2 (Python / Notebook):**
- Python 3.10+
- Google Colab or Jupyter Notebook

**Python packages:**
```
pandas
numpy
category_encoders
scikit-learn
matplotlib
seaborn
```

---

## 🗂️ Project Structure

```
BA-Data-Science-Job-Simulation/
├── Task1_Lounge_Eligibility/
│   ├── Lounge_Eligibility_Lookup_Template.xlsx
│   └── flight_data.csv                          # Summer Schedule dataset (Forage-provided)
├── Task2_Predicting_Customer_Buying_Behaviour/
│   ├── BA_Task2_PredictingCustomerBuyingBehaviour.ipynb
│   └── customer_booking.csv                      # Forage-provided booking dataset
└── results/                                       # exported charts (see Visualization)
```

---

## 📊 Data

**Task 1 — Flight Schedule Dataset**
- Source: British Airways Summer Schedule dataset (Forage-provided), **10,000 flights**
- Fields used: haul type (long/short), time of day, `TIER1_ELIGIBLE_PAX`, `TIER2_ELIGIBLE_PAX`, `TIER3_ELIGIBLE_PAX`, total seat capacity
- Tier percentages = total eligible passengers ÷ total seat capacity, per group

**Task 2 — Customer Booking Dataset**
- Source: `customer_booking.csv` (Forage-provided), **50,000 rows, 14 original columns**
- Target: `booking_complete` (binary) — **15.0% positive class** (imbalanced)
- No missing values in any column
- High-cardinality categoricals: `route` (799 unique values), `booking_origin` (104 unique values)

---

## 🛠️ Installation

```bash
# 1. Clone the repository
git clone <your-repo-url>
cd <your-repo-folder>

# 2. Install dependencies for Task 2
pip install -q pandas numpy category_encoders scikit-learn matplotlib seaborn

# 3. Open Task 1 in Excel/Sheets, or Task 2 in Jupyter/Colab
jupyter notebook Task2_Predicting_Customer_Buying_Behaviour/BA_Task2_PredictingCustomerBuyingBehaviour.ipynb
```

---

## 🧭 Project Methodology

### Task 1 — Lounge Eligibility Lookup
1. Grouped all 10,000 flights by **haul type** (long/short) × **time of day** (morning / lunchtime / afternoon / evening) → 8 groups
2. Used the dataset's existing `TIER1/2/3_ELIGIBLE_PAX` columns directly — no custom eligibility rules invented
3. Calculated Tier 1 / 2 / 3 percentages per group = eligible passengers ÷ total seat capacity
4. Documented grouping rationale, assumptions, and how the lookup table generalizes to future schedules (see the `Justification` sheet)

### Task 2 — Predicting Customer Buying Behaviour
1. **EDA** — checked structure, missing values (none), class balance (15% completion rate), cardinality of categorical fields
2. **Feature engineering** — 5 new binary/count features:
   - `is_weekend_flight`, `is_early_morning` (before 08:00), `books_within_30_days` (purchase lead ≤ 30 days), `long_stay` (> 14 nights), `total_extras` (sum of baggage/seat/meal add-ons)
3. **Preprocessing pipeline** — `TargetEncoder` for high-cardinality `route`/`booking_origin`, `OneHotEncoder` for `sales_channel`/`trip_type`, remaining numeric features passed through
4. **Model** — `RandomForestClassifier` (200 trees) inside an sklearn `Pipeline`
5. **Validation** — 5-fold `StratifiedKFold` cross-validation, encoder fit only on each fold's training split (no leakage)
6. **Threshold tuning** — evaluated precision/recall trade-off across thresholds 0.10–0.50; selected **0.20** to prioritize recall (catching more likely bookers) given the class imbalance
7. **Final evaluation** — trained on full training set, evaluated once on the held-out test set
8. **Feature importance** — extracted and ranked from the fitted Random Forest

**Cross-validation results (5-fold):**

| Fold | Precision | Recall | ROC-AUC |
|---|---|---|---|
| 1 | 0.3243 | 0.6773 | 0.7845 |
| 2 | 0.3194 | 0.6530 | 0.7748 |
| 3 | 0.3141 | 0.6731 | 0.7822 |
| 4 | 0.3160 | 0.6391 | 0.7785 |
| 5 | 0.3209 | 0.6842 | 0.7874 |
| **Mean ± SD** | **0.3189 ± 0.0040** | **0.6653 ± 0.0187** | **0.7815 ± 0.0050** |

**Final test set results (threshold = 0.20):**

| Metric | Score |
|---|---|
| Precision | 0.3258 |
| Recall | 0.6818 |
| ROC-AUC | 0.7902 |
| Accuracy | 0.74 |

**Business framing of the final result:** targeting the top 31.31% of customers by predicted probability captures 68.18% of actual bookers, at the cost of also targeting some non-bookers — a deliberate recall-favoring trade-off suited to a marketing/targeting use case.

---

## 📈 Visualization

Generated during the Task 2 notebook run:

- 📊 **Booking completion bar chart** — class imbalance (15% completed vs. 85% not completed)
- 📉 **ROC-AUC & Recall across CV folds** — grouped bar chart comparing all 5 folds
- 🧩 **Confusion matrix** — final predictions at threshold 0.20
- 📶 **Top 15 feature importances** — horizontal bar chart from the fitted Random Forest
- 🎚️ **Precision/Recall vs. threshold table** — trade-off across thresholds 0.10 to 0.50

*(Add your actual exported chart images here once saved to a `results/` folder, e.g. `![Confusion Matrix](results/confusion_matrix.png)` — happy to wire these in with real filenames, same as the other projects.)*

---

## 🧰 Technology Used

| Category | Tools |
|---|---|
| Data manipulation | ![Pandas](https://img.shields.io/badge/Pandas-150458?logo=pandas&logoColor=white) ![NumPy](https://img.shields.io/badge/NumPy-013243?logo=numpy&logoColor=white) |
| Encoding | ![category_encoders](https://img.shields.io/badge/category__encoders-TargetEncoder-orange) `OneHotEncoder` |
| Modeling | ![scikit-learn](https://img.shields.io/badge/scikit--learn-RandomForest-F7931E?logo=scikitlearn&logoColor=white) |
| Validation | `StratifiedKFold` cross-validation |
| Visualization | ![Matplotlib](https://img.shields.io/badge/Matplotlib-11557C) ![Seaborn](https://img.shields.io/badge/Seaborn-3776AB) |
| Spreadsheet analysis | ![Excel](https://img.shields.io/badge/Excel-217346?logo=microsoftexcel&logoColor=white) |
| Environment | ![Colab](https://img.shields.io/badge/Google_Colab-F9AB00?logo=googlecolab&logoColor=white) |

---

## 🚀 Future Improvements

- 🔀 Try gradient-boosted models (XGBoost, LightGBM) as a stronger baseline than Random Forest
- 🧮 Address class imbalance directly with `class_weight='balanced'` or SMOTE, rather than relying solely on threshold tuning
- 🗺️ Reduce `route`/`booking_origin` cardinality risk by testing alternative encodings (frequency encoding, embeddings) alongside target encoding
- 📅 Extend Task 1's lookup table with a full-year dataset to capture seasonality, not just the Summer Schedule
- 🔗 Combine both tasks — use Task 2's booking-completion predictions to refine Task 1's lounge eligibility estimates for future schedules
- 📦 Package the Task 2 pipeline (preprocessing + model) for reuse on new booking data via a simple `predict()` function

---

<div align="center">
Completed as part of the British Airways Data Science Job Simulation on Forage
</div>
