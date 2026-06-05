# 🏦 Banking Risk Analytics Dashboard

> An end-to-end data analytics project combining **Exploratory Data Analysis (Python)** and an **interactive Power BI Dashboard** to uncover customer behavior, loan exposure, and deposit patterns across a bank's 3,000-client portfolio.

---

## 📌 Table of Contents

- [Project Overview](#project-overview)
- [Dataset Description](#dataset-description)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
  - [Feature Engineering](#feature-engineering)
  - [Univariate Analysis](#univariate-analysis)
  - [Bivariate Analysis](#bivariate-analysis)
  - [Numerical Analysis & Correlation](#numerical-analysis--correlation)
- [Power BI Dashboard](#power-bi-dashboard)
  - [Home Page](#home-page)
  - [Loan Analysis Page](#loan-analysis-page)
  - [Deposit Analysis Page](#deposit-analysis-page)
  - [Summary Page](#summary-page)
- [Key Business Insights](#key-business-insights)
- [Future Scope](#future-scope)
- [Author](#author)

---

## 📖 Project Overview

This project performs a comprehensive risk and behavioral analysis on a banking dataset of **2,913 active clients**. The goal is to understand:

- How loans and deposits are distributed across customer segments
- Which customer profiles carry the highest financial risk
- What drives loyalty classification and fee structure
- How income bands relate to borrowing and saving behavior

The project is divided into two parts:
1. **Python EDA** — data exploration, feature engineering, and statistical analysis
2. **Power BI Dashboard** — 4-page interactive dashboard for business stakeholders

---

## 📂 Dataset Description

**Size:** 3,000 rows × 25 columns | Zero null values

| Column | Type | Description |
|--------|------|-------------|
| `BRId` | Categorical | Banking Relationship ID (Commercial / Institutional / Private Bank / Retail) |
| `GenderId` | Categorical | Customer gender |
| `IAId` | Categorical | Investment Advisor ID (22 unique advisors) |
| `Nationality` | Categorical | European, Asian, American, Australian, African |
| `Occupation` | Categorical | 195 unique occupations |
| `Fee Structure` | Categorical | High / Mid / Low |
| `Loyalty Classification` | Categorical | Jade / Silver / Gold / Platinum |
| `Risk Weighting` | Ordinal | 1 (Lowest Risk) → 5 (Highest Risk) |
| `Properties Owned` | Ordinal | 0 to 3 properties |
| `Amount of Credit Cards` | Ordinal | 1, 2, or 3 cards |
| `Estimated Income` | Numerical | Annual estimated income |
| `Superannuation Savings` | Numerical | Retirement savings |
| `Credit Card Balance` | Numerical | Outstanding CC balance |
| `Bank Loans` | Numerical | Personal/bank loan amount |
| `Bank Deposits` | Numerical | Total bank deposit amount |
| `Checking Accounts` | Numerical | Checking account balance |
| `Saving Accounts` | Numerical | Savings account balance |
| `Foreign Currency Account` | Numerical | Foreign currency holdings |
| `Business Lending` | Numerical | Business loan amount |
| `Joined Bank` | Date | Customer onboarding date |

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **Python 3** | EDA, data manipulation, visualization |
| **Pandas** | Data loading, transformation, feature engineering |
| **Matplotlib & Seaborn** | Histplots, countplots, heatmaps |
| **NumPy** | Numerical operations |
| **Power BI Desktop** | Interactive 4-page dashboard |
| **Jupyter Notebook** | EDA development environment |

---

## 📁 Project Structure

```
Banking Risk Analytics/
│
├── Banking Dashboard -Amam Jain.png
├── Banking Dashboard Deposit Analysis-Amam Jain.png
├── Banking Dashboard Loan Analysis -Amam Jain.png
├── Banking Dashboard Summary Page -Amam Jain.png
├── Banking Dashboard.pbix                         # Power BI dashboard file
├── Banking.xlsx                                   # Raw dataset
├── banking-realtionships.xlsx                     # Banking relationship lookup table
├── investment-advisiors.xlsx                      # Investment advisor lookup table
├── BankingRiskAnalytics_EDA.ipynb                 # Python EDA notebook
└── README.md
```

---

## 🔍 Exploratory Data Analysis (EDA)

### Feature Engineering

A new column **`Income Band`** was created by binning `Estimated Income` into three segments:

```python
bins   = [0, 100000, 300000, float('inf')]
labels = ['Low income', 'Medium income', 'High income']

bank_data['Income Band'] = pd.cut(
    bank_data['Estimated Income'],
    bins=bins,
    labels=labels,
    right=False
)
```

| Income Band | Count | % of Customers |
|-------------|-------|----------------|
| Medium income (₹1L – ₹3L) | 1,517 | 50.6% |
| Low income (< ₹1L) | 1,027 | 34.2% |
| High income (> ₹3L) | 456 | 15.2% |

> **Insight:** Over 84% of customers fall below ₹3L income. The high-income segment, though only 15.2%, holds disproportionate financial weight.

---

### Univariate Analysis

Distribution of all categorical columns was plotted using `sns.countplot()`:

```python
for i, predictor in enumerate(categorical_columns):
    plt.figure(i)
    sns.countplot(data=bank_data, x=predictor)
```

**Key observations:**

| Column | Top Category | Count |
|--------|-------------|-------|
| Nationality | European | 1,309 (43.6%) |
| Loyalty Classification | Jade | 1,331 (45.7%) |
| Risk Weighting | 2 | 1,222 (40.7%) |
| Fee Structure | High | 1,476 (49.2%) |
| Amount of Credit Cards | 1 card | 1,922 (64.1%) |
| Gender | Near equal | Female: 1,512 / Male: 1,488 |

---

### Bivariate Analysis

Each categorical column was cross-analyzed against `Nationality` as the hue:

```python
for i, predictor in enumerate(categorical_columns):
    plt.figure(i)
    sns.countplot(data=bank_data, x=predictor, hue='Nationality')
```

**Key observations:**
- Europeans dominate across all categories — consistent with being the largest nationality group
- No single nationality shows extreme outlier behavior in any category
- Nationality is a **weak predictor** of financial behavior — distributions are proportional to group size

---

### Numerical Analysis & Correlation

All 9 numerical columns visualized using histplots with KDE:

```python
plt.figure(figsize=(15, 10))
for i, col in enumerate(numerical_columns):
    plt.subplot(4, 3, i+1)
    sns.histplot(bank_data[col], kde=True)
    plt.title(col)
plt.show()
```

**Skewness Summary:**

| Column | Mean | Median | Skewness |
|--------|------|--------|----------|
| Estimated Income | ₹1,71,305 | ₹1,42,313 | 0.88 |
| Superannuation Savings | ₹25,532 | ₹22,357 | 0.71 |
| Credit Card Balance | ₹3,176 | ₹2,561 | 1.13 |
| Bank Loans | ₹5,91,386 | ₹4,79,793 | 1.18 |
| Bank Deposits | ₹6,71,560 | ₹4,63,316 | 1.71 |
| Checking Accounts | ₹3,21,093 | ₹2,42,816 | 1.73 |
| Saving Accounts | ₹2,32,908 | ₹1,64,087 | 2.19 |
| Foreign Currency | ₹29,884 | ₹24,341 | 1.12 |
| Business Lending | ₹8,66,760 | ₹7,11,315 | 1.15 |

> ⚠️ All financial columns are right-skewed. Log transformation recommended before ML modeling.

**Top Correlations from Heatmap:**

| Feature Pair | Correlation | Interpretation |
|-------------|-------------|----------------|
| Bank Deposits ↔ Checking Accounts | **0.84** | Wealthy customers spread funds across accounts |
| Bank Deposits ↔ Saving Accounts | **0.75** | High deposit holders also save more |
| Bank Loans ↔ Business Lending | **0.42** | Business borrowers also take personal loans |
| Bank Deposits ↔ Business Lending | **0.44** | Business clients maintain higher deposits |
| Estimated Income ↔ Superannuation | **0.37** | Higher earners save more for retirement |
| Estimated Income ↔ Bank Deposits | **0.26** | High income does not guarantee high deposits |

---

## 📊 Power BI Dashboard

4-page interactive dashboard with consistent filters across all pages:
- **Joining Year:** 2013, 2018, 2019, 2020, 2021
- **Banking Relationship:** Commercial, Institutional, Private Bank, Retail
- **Gender:** Female / Male
- **Investment Advisor:** Dropdown (All or specific advisor)

---

### Home Page

| KPI | Value |
|-----|-------|
| Total Clients | 2,913 |
| Total Loan | 4.38bn |
| Total Deposit | 3.77bn |
| Checking Accounts | 963.28M |
| Savings Account | 698.73M |
| Business Lending | 2.60bn |

> **Critical Insight:** Total Loan (4.38bn) exceeds Total Deposit (3.77bn) — the bank is in a **net lending position**.

---

### Loan Analysis Page

| KPI | Value |
|-----|-------|
| Total Loan | 4.38bn |
| Bank Loan | 1.77bn (40.4%) |
| Business Lending | 2.60bn (59.4%) |
| CC Balance | 9.53M (0.2%) |

**Key Insights:**
- Business Lending (59.4%) dominates — this is fundamentally a **commercial banking** portfolio
- Credit Card exposure is negligible at 0.2% — minimal CC risk
- Medium income customers hold ~55% of total loans

---

### Deposit Analysis Page

| KPI | Value |
|-----|-------|
| Total Deposit | 3.77bn |
| Bank Deposit | 2.01bn (53.3%) |
| Checking Account | 963.28M (25.5%) |
| Savings Account | 698.73M (18.5%) |

**Bank Deposit by Income Band:**

| Income Band | Deposit Amount | Share |
|-------------|---------------|-------|
| Medium income | 1.09bn | 54.17% |
| High income | 0.50bn | 24.79% |
| Low income | 0.42bn | 21.05% |

**Key Insights:**
- High-income clients are 15.2% of customers but hold **24.79% of deposits**
- Checking Accounts (963M) > Savings Accounts (699M) — customers prefer liquidity

---

### Summary Page

| KPI | Value |
|-----|-------|
| Total Client | 2,913 |
| Total Loan | 4.38bn |
| Bank Loan | 1.77bn |
| Business Lending | 2.60bn |
| Total Deposit | 3.77bn |
| Bank Deposit | 2.01bn |
| Checking Account | 963.28M |
| Saving Account | 698.73M |
| Foreign Currency | 89.65M |
| Total CC Amount | 4K |

---

## 💡 Key Business Insights

**1. 🔴 Loan > Deposit — Liquidity Risk Signal**
Total loans (4.38bn) exceed deposits (3.77bn). If significant loans default simultaneously, the bank faces liquidity pressure.

**2. 💼 Commercial Portfolio Dominance**
Business Lending = 59.4% of all loans. This is a **commercial lending** portfolio — risk frameworks should be calibrated accordingly.

**3. 💎 High-Income Segment — Small but Mighty**
15.2% of customers hold ~25% of deposits. A dedicated **wealth management** product line could significantly increase revenue.

**4. 📈 Risk Weighting Scales With Income**
Higher income = higher risk weighting. "Risk" here means financial exposure/complexity, not credit default risk.

| Risk Weight | Avg Income | Avg Bank Loan | Avg CC Balance |
|------------|-----------|---------------|----------------|
| 1 | ₹77,540 | ₹3.34L | ₹1,882 |
| 3 | ₹2,46,058 | ₹7.87L | ₹4,110 |
| 5 | ₹3,10,041 | ₹9.75L | ₹5,300 |

**5. 💳 Checking Over Savings — Low Savings Culture**
Customers prefer liquid checking accounts (963M) over savings (699M). Opportunity to promote FDs and savings schemes.

**6. 🏆 Loyalty Pyramid is Bottom-Heavy**
72% of customers are Jade or Silver tier. Only 317 (10.9%) are Platinum. A loyalty upgrade program can grow the premium base.

**7. 🌍 Nationality Has Minimal Impact**
All nationalities show within ±5% variation across financial metrics. Nationality is a **poor predictor** and should be excluded from ML models.

---

## 🚀 Future Scope

| Enhancement | Description |
|------------|-------------|
| **Risk Prediction Model** | ML classification to predict Risk Weighting (1–5) using income, loans, CC balance |
| **Loyalty Tier Prediction** | Predict customer upgrade from Jade → Silver → Gold → Platinum |
| **Customer Segmentation** | K-Means clustering on financial behavior |
| **Churn Analysis** | Use `Joined Bank` date to compute tenure; identify early churn risk |
| **Log Transformation** | Apply to all skewed numerical columns before ML modeling |
| **Time Series Trends** | Month-level trend charts in Power BI |
| **Loan-to-Deposit Ratio KPI** | Explicit LDR card on dashboard for liquidity monitoring |

---

## 👤 Author

**Amam Jain**
Aspiring Data Analyst | Power BI · Python · SQL · EDA

📧 amamjain09@gmail.com
🔗 [LinkedIn](https://www.linkedin.com/in/amam-jain-216082386)
🐙 [GitHub](https://github.com/AmamJainn)

---

> ⭐ If you found this project useful, consider giving it a star on GitHub!
