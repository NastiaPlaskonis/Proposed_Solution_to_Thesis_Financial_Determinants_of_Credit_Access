# Credit Access for Ukrainian Firms: Financial Determinants and Predictive Modelling

---

## Author

**Anastasiia PLASKONIS**  
Bachelor's Thesis, Ukrainian Catholic University,
2026
---

This repository contains the proposed solution developed for the thesis *"Credit Access for Ukrainian Firms: Financial Determinants and Predictive Modelling"*. The analysis uses firm-level financial panel data for Ukrainian medium-to-large enterprises over the period 2018-2024.

---

## ABSTRACT

A profitable corporate sector generates tax revenues that underpin public finances, including defence spending, which has become critical during the full-scale invasion. To remain profitable and grow, firms need to invest in equipment, working capital and operational capacity. With equity markets largely closed, bank credit is one of the few available sources of external financing to fund these investments. Understanding which firms access bank credit and what financial characteristics determine this access is therefore a question of direct macroeconomic and policy importance.

The purpose of this study is to identify the financial determinants of bank credit access among medium-sized and large Ukrainian firms over the period 2019-2024, and to model the probability of increasing credit, using both logistic regression and machine learning approaches. The analysis is based on a panel of 81,062 observations across 18,119 firms. The dependent variable is defined as a year-on-year increase in net bank debt. The main method is pooled logistic regression with firm-clustered standard errors. Two machine learning models, Random Forest and Gradient Boosting, are estimated as additional approaches and benchmarked against the logit baseline. 

The results show, even under wartime conditions, credit allocation in Ukraine remains risk-based. What drives credit access is not just collateral or size, but firm profitability and whether a firm already carries bank debt. The two crises had different effects. During COVID-19, firms prioritised liquidity retention, while the full-scale war led banks to screen borrowers more heavily on profitability. In regions with prolonged war exposure since 2014, financial metrics lose predictive power and relationship-based lending dominates. In regions affected since 2022, profitability becomes an even stronger determinant of credit access than in non-frontline regions. Credit access determinants also vary by sector: profitability and collateral drives lending in agriculture, liquidity and working capital in manufacturing and wholesale trade, operational efficiency in energy and profitability in mining.

The findings offer empirical evidence on credit risk assessment under wartime and identify the financial profiles of firms most likely to qualify for bank credit. This information can serve as a basis for designing targeted lending programs for policymakers and international financial institutions.

---

## Data

The underlying dataset is sourced from **YouControl Market** and covers medium-to-large Ukrainian firms across all major sectors. The data are **not included in this repository** due to confidentiality constraints.

Full information about the dataset — including its structure, variable definitions, data quality checks, and sample construction decisions — is documented in:

- **Notebook 01** — raw data loading, variable types, and initial inspection
- **Notebook 02** — data cleaning pipeline, balance sheet integrity checks, and the construction of the final analytical sample

---

## Repository structure

```
├── 01_data_loading.ipynb
├── 02_data_overview.ipynb
├── 03_dependent_variables.ipynb
├── 04_eda_financial_indicators.ipynb
├── 05_controls_heterogeneity.ipynb
├── 06_statistical_tests_logit.ipynb
└── 07_predictive_component.ipynb
```

---

## Notebooks

### 01 Data loading
Loading the raw CSV export from YouControl Market. Covers variable type casting, initial row counts, and firm-level observation frequency. Includes a note on data confidentiality.

Step-by-step cleaning pipeline:
- Duplicate removal
- Dropping observations with missing key variables
- Removing economically invalid negative values
- Eleven balance sheet integrity checks
- Minimum two observations per firm requirement
- Consecutive year pair construction

Saves the cleaned dataset as `df_cleaned.csv`.

### 02 Data overview
Descriptive overview of the analytical sample:
- Firm status and age distributions
- Sector structure (NACE classification, top-5 sector dynamics)
- Regional analysis with frontline region classification (2014 and 2022 waves)
- Ukraine choropleth map (GADM shapefile)

### 03 Dependent Variables
Construction and analysis of three binary credit access proxies:

| Variable | Definition |
|---|---|
| **Y1** — Has Credit | Total Bank Debt > 0 |
| **Y2** — Active Credit User | Has credit AND debt change ≥ −20% |
| **Y3** — Increasing Credit | Total Debt at t > Total Debt at t−1 *(main)* |

Includes credit dynamics classification (New Credit / Increasing / Stable / Actively Repaying / Fully Repaid), portfolio charts, and sector/region breakdowns.

### 04 EDA: Financial indicators
Exploratory analysis of 20 winsorised financial indicators across 8 groups:

| Group | Indicators |
|---|---|
| Size | log(Total Assets), log(Net Revenue) |
| Profitability | EBIT Margin, EBITDA Margin, PBT Margin, ROA, GP Margin |
| Liquidity | Current Ratio, Quick Ratio, Cash Ratio |
| Leverage | Equity Ratio, Net Debt/EBITDA, Debt to Assets |
| Debt Service | ICR (EBIT) |
| Collateral | Tangibility (PPE/Total Assets) |
| Activity | Asset Turnover, Inventory Days, AR Days, AP Days |
| Working Capital | WC/Total Assets |
| Age |

All predictors are measured at t−1 (one-year lag) to address endogeneity. Includes group comparison tables, KDE/violin/box plots, sector difference bar charts, and dynamics by sector over time.

### 05 Controls and heterogeneity analysis
Analysis of control variables and heterogeneity:
- War, COVID, and frontline dummies by dependent variable and sector
- Year-over-year heatmap of financial indicators (all years + crisis years by credit group)
- Crisis years heatmap by sector (Y3=1 and Y3=0 separately)
- Share of firms with increasing credit in crisis years by sector
- Regional credit access before and after the full-scale war
- Frontline dummy analysis by sector

### 06 Statistical tests and logit models
Full modelling pipeline:

**Statistical tests**
- Mann-Whitney U tests for all financial indicators
- Spearman correlation matrix on lagged variables
- Point-biserial correlation and univariate logit

**Main logit models (Model-1–Model-5)**
Progressive specifications from baseline to full model with sector and frontline fixed effects. Year FE included in all models (ref = 2018). Standard errors clustered by Firm ID.

**Alternative specifications (Alternative1–Alternative-5)**
Systematic comparison across profitability (EBIT/EBITDA/ROA), liquidity (Quick/Current), and activity (Asset Turnover/log Revenue/log Assets) groups. Winner: **Alternative-22_ROA**.

**Final model**
Alternative-22_ROA significant predictors only. Includes average marginal effects plot.

**Robustness checks (R1–R5)**
- R1: Y1 (Has Credit) as dependent variable
- R2: Y2 (Active Credit User) as dependent variable
- R3: Debt/Assets instead of Net Debt/EBITDA
- R4: Active firms only
- R5: Crisis dummies only (no full Year FE)

**Heterogeneity analysis**
- Crisis interaction model (COVID × X and War × X)
- Subperiod analysis (2020–2021 vs 2022–2024)
- Regional models (non-frontline / Frontline 2014 / Frontline 2022)
- Sectoral models (Agriculture, Manufacturing, Wholesale & Retail Trade, Energy, Mining, Other)
- ICR supplementary models (Energy and Mining)

### 07 Predictive component
Temporal validation comparison of three models:

| Model | Feature set | Time control |
|---|---|---|
| Logit | Alternative-2_ROA full set + sector dummies | No Year FE |
| Random Forest | Alternative-2_ROA full set + sector dummies | — |
| XGBoost | Alternative-2_ROA full set + sector dummies | — |

**Split:** Train 2019–2021 · Test 2022–2024

RF and XGBoost tuned via `RandomizedSearchCV` (30 iterations, 5-fold stratified CV). RF uses `class_weight='balanced'`; XGBoost uses `scale_pos_weight`.

Outputs: comparison table (AUC-ROC, AUC-PR, F1, Precision, Recall, Balanced Accuracy, Brier, KS), feature importance chart (RF vs XGBoost), ROC curves, and confusion matrices at Youden's J optimal threshold.

---

## Key methodological decisions

- **Dependent variable:** Y3 (Increasing Credit) is the main specification; Y1 and Y2 serve as robustness checks
- **Predictor lag:** all financial indicators measured at t−1
- **Preferred model:** Alternative-2_ROA — replaces EBIT Margin with ROA, avoids VIF > 5 present in size-based alternatives
- **Time fixed effects:** full Year FE in all main logit models; no Year FE in predictive logit (temporal split logic)
- **Standard errors:** clustered by Firm ID throughout
- **Winsorisation:** 1% for log/bounded indicators; 5% for ratios

---

## Requirements

```
pandas
numpy
matplotlib
scipy
statsmodels
scikit-learn
xgboost
geopandas          # Notebook 02 (choropleth map)
```
