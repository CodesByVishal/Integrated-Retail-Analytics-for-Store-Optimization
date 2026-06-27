# Integrated Retail Analytics — Store Optimization & Demand Forecasting
---

## Project Overview

End-to-end analytics pipeline applied to a retail chain with 45 stores, 99 departments, and 421,570 weekly sales records spanning 2010–2013. The project addresses three core operational questions: which stores and departments are underperforming, when should promotional markdowns be deployed, and how much stock should be ordered 3 months ahead.

---

## Dataset

| File | Rows | Description |
|---|---|---|
| `sales data-set.csv` | 421,570 | Weekly sales per store-department — TARGET: Weekly_Sales |
| `stores data-set (1).csv` | 45 | Store metadata: Type (A/B/C), Size (sq ft) |
| `Features data set (1).csv` | 8,190 | CPI, Fuel_Price, Unemployment, Temperature, MarkDown1–5 |

All three join on `Store` + `Date` keys.

---

## Pipeline — 6 Phases

### Phase 1: Exploratory Data Analysis
- Holiday effect: +20% sales spike in Nov–Dec (Thanksgiving, Christmas, Black Friday)
- January post-holiday dip: approximately -15%
- Store rankings: Stores 20 and 4 are top earners; Stores 44 and 33 are lowest
- Top 5 stores generate ~3x the revenue of the bottom 5
- MarkDown3 peaks in November — promotions are deliberately timed

### Phase 2: Data Preprocessing
- Median imputation for MarkDown1–5 (60–70% nulls — valid missingness, no promotion = no markdown)
- IQR capping at 1.5x for markdown outliers
- Square root transformation on Weekly_Sales to reduce skewness
- StandardScaler on all numeric features
- Datetime decomposition: year, month, day-of-week extracted

### Phase 3: Anomaly Detection (3 methods — all converge)
- **Isolation Forest** (contamination=0.05): ~21,000 holiday-week anomalies flagged
- **Rolling Statistics** (30-period window): anomalies at holiday and markdown events
- **EWMA** (alpha=0.2): 3 annual spike clusters confirmed

### Phase 4: Customer Segmentation — PCA + KMeans
- PCA: 90% variance retained in 2–3 principal components
- Elbow Method + Silhouette Score: K=4 optimal for both stores and departments
- **4 Store Clusters:** Premium & Luxury / Value-Oriented Mid-Range / Budget & Discount / Efficient Mid-Premium
- **4 Department Clusters:** Sizeable Luxury / Premium Select / Elite Boutique / Volume-Driven

### Phase 5: Market Basket Analysis — Apriori
- min_support=0.011, min_confidence=0.80, min_lift=3.0
- Identifies department co-purchase pairs for store layout and bundle promotions

### Phase 6: Demand Forecasting

| Model | Best Use | Key Strength |
|---|---|---|
| SARIMAX (s=52, exog=5 economic vars) | 1–4 week tactical | Interpretable + holiday-explicit |
| Holt-Winters (additive, s=52) | Full-year planning | Stable 52-week projection |
| Random Forest (lag + store + economic features) | Store-level granular | Non-linear interactions |

---

## Key Results

| Metric | Value |
|---|---|
| Holiday sales surge | +20% Nov–Dec |
| Post-holiday January dip | -15% |
| Optimal store clusters | K=4 |
| Optimal department clusters | K=4 |
| Top store earner | Store 20 (Type A) |
| Top external predictor | Temperature (RF feature importance) |

---

## Tech Stack

```
Python | Pandas | NumPy | Scikit-learn | Statsmodels | Matplotlib | Seaborn
SARIMAX | ExponentialSmoothing | IsolationForest | KMeans | PCA | Apriori (mlxtend)
```

---

## Repository Contents

```
Integrated-Retail-Analytics-for-Store-Optimization/
├── Integrated_Retail_Analytics (1).ipynb      # Main analysis notebook
├── sales data-set.csv                          # Weekly sales (421K rows)
├── Features data set (1).csv                  # Economic indicators
├── stores data-set (1).csv                    # Store metadata
└── Retail_Analytics_Presentation.pptx         # Project presentation (25 slides)
```

---

## How to Run

```bash
pip install pandas numpy scikit-learn statsmodels mlxtend matplotlib seaborn
jupyter notebook "Integrated_Retail_Analytics.ipynb"
```

---

*AlmaBetter M.Sc. Data Science | 2026 | Vishal Kumar Singh*
