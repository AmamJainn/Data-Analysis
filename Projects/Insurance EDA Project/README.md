# Insurance Cost EDA & Feature Engineering Project

Exploratory Data Analysis, data cleaning, feature engineering, and statistical
feature selection performed on the classic **Medical Cost Personal Insurance**
dataset (`insurance.csv`), with the goal of preparing a clean, model-ready
dataset for predicting insurance **charges**.

---

## 1. Dataset Overview

| Property | Value |
|---|---|
| Source file | `insurance.csv` |
| Rows (raw) | 1,338 |
| Rows (after de-duplication) | 1,337 |
| Columns (raw) | 7 |
| Target variable | `charges` (medical insurance cost, continuous) |

**Raw columns:**

| Column | Type | Description |
|---|---|---|
| `age` | numeric | Age of the primary beneficiary |
| `sex` | categorical | `male` / `female` |
| `bmi` | numeric | Body Mass Index |
| `children` | numeric | Number of dependents covered |
| `smoker` | categorical | `yes` / `no` |
| `region` | categorical | Residential area (`northeast`, `northwest`, `southeast`, `southwest`) |
| `charges` | numeric | Individual medical costs billed by insurance (target) |

**Class balance in the raw data:**
- `sex`: 675 male, 662 female — well balanced
- `smoker`: 1,063 non-smokers, 274 smokers — imbalanced (~20% smokers)
- `region`: fairly even split (southeast 364, southwest 325, northwest 324, northeast 324)

---

## 2. Project Workflow

The notebook (`Insurance_EDA_Project.ipynb`) is organized into four stages:

### Stage 1 — Exploratory Data Analysis (EDA)
- Loaded the dataset and inspected shape, dtypes, summary statistics, and
  null values (dataset has **no missing values**).
- Plotted **histograms with KDE** for all numeric columns (`age`, `children`,
  `bmi`, `charges`) to inspect distributions.
- Plotted **count plots** for categorical columns (`children`, `sex`, `smoker`)
  to check class balance.
- Plotted **box plots** for numeric columns to check for outliers
  (`charges` and `bmi` show visible outliers/right-skew).
- Plotted a **correlation heatmap** of the raw numeric features.

### Stage 2 — Data Cleaning & Preprocessing
- Created a working copy (`df_cleaned`) to preserve the original data.
- Removed duplicate rows with `drop_duplicates()` → row count dropped from
  **1,338 to 1,337** (1 duplicate found and removed).
- Confirmed no missing values remained after cleaning.
- Encoded categorical variables:
  - `sex` → mapped to binary and renamed to **`is_female`** (`male`=0, `female`=1)
  - `smoker` → mapped to binary and renamed to **`is_smoker`** (`no`=0, `yes`=1)
  - `region` → **one-hot encoded** with `drop_first=True`, producing
    `region_northwest`, `region_southeast`, `region_southwest`
    (`northeast` is the implicit baseline/reference category)
- Cast all columns to integer type for consistency.

### Stage 3 — Feature Engineering & Extraction
- Engineered a new categorical feature **`bmi_category`** by binning `bmi`
  into standard clinical ranges:
  | Range | Category |
  |---|---|
  | 0 – 18.5 | Underweight |
  | 18.5 – 24.9 | Normal |
  | 24.9 – 29.9 | Overweight |
  | 29.9+ | Obese |
- One-hot encoded `bmi_category` (`drop_first=True` → `Underweight` is the
  baseline; `bmi_category_Normal`, `bmi_category_Overweight`,
  `bmi_category_Obese` retained).
- Applied **StandardScaler** to scale `age`, `bmi`, and `children` to
  zero mean / unit variance.

### Stage 4 — Statistical Feature Selection
Two statistical tests were used to validate which features are meaningfully
associated with the target before finalizing the feature set.

**a) Pearson correlation** (numeric/binary features vs. `charges`):

| Feature | Correlation with `charges` |
|---|---|
| `is_smoker` | **0.787** (strongest predictor by far) |
| `age` | 0.298 |
| `bmi_category_Obese` | 0.200 |
| `bmi` | 0.196 |
| `region_southeast` | 0.074 |
| `children` | 0.067 |
| `region_northwest` | -0.039 |
| `region_southwest` | -0.044 |
| `is_female` | -0.058 |
| `bmi_category_Normal` | -0.104 |
| `bmi_category_Overweight` | -0.121 |

**b) Chi-squared test of independence** (categorical features vs. `charges`
binned into quartiles, α = 0.05):

| Feature | p-value | Decision |
|---|---|---|
| `is_smoker` | ~0.0 | Keep |
| `region_southeast` | 0.0011 | Keep |
| `is_female` | 0.0165 | Keep |
| `bmi_category_Obese` | 0.0365 | Keep |
| `region_southwest` | 0.165 | Drop |
| `bmi_category_Overweight` | 0.236 | Drop |
| `bmi_category_Normal` | 0.295 | Drop |
| `region_northwest` | 0.769 | Drop |

### Final Feature Set
Based on the combined correlation and chi-squared analysis, the final
model-ready dataframe (`final_df`) retains:

```
age, is_female, bmi, children, is_smoker, charges,
region_southeast, bmi_category_Obese
```

---

## 3. Key Findings

- **Smoking status is the single strongest driver of insurance charges**
  (Pearson r ≈ 0.79) — smokers are billed dramatically more than non-smokers.
- **Age** and **obesity** (`bmi_category_Obese`) are moderate, statistically
  significant predictors of higher charges.
- **Region** has a mild but statistically significant effect only for
  `region_southeast`; the other regional dummies were not significant and
  were dropped from the final feature set.
- **Sex** (`is_female`) shows a weak but statistically significant
  association with `charges` under the chi-squared test, despite a low
  Pearson correlation — worth keeping as a categorical control variable.
- `bmi` as a continuous variable and most `bmi_category` bins beyond
  "Obese" did not add significant explanatory power once other features
  were accounted for.

---

## 4. Project / File Structure

```
.
├── Insurance_EDA_Project.ipynb   # Main analysis notebook
├── insurance.csv                 # Raw dataset (must be placed alongside notebook)
└── README.md                     # This file
```

---

## 5. Requirements

```
python >= 3.8
pandas
numpy
matplotlib
seaborn
scikit-learn
scipy
```

Install with:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy
```

## 6. How to Run

1. Place `insurance.csv` in the same directory as the notebook.
2. Launch Jupyter:
   ```bash
   jupyter notebook Insurance_EDA_Project.ipynb
   ```
3. Run all cells top to bottom. The notebook will produce:
   - Distribution and outlier plots for all numeric features
   - A correlation heatmap
   - A cleaned, encoded, and scaled dataframe (`df_cleaned`)
   - Pearson correlation and Chi-squared significance tables
   - A final, statistically-vetted, model-ready dataframe (`final_df`)

---

## 7. Possible Next Steps

- Train regression models (Linear Regression, Random Forest, XGBoost) on
  `final_df` to predict `charges`.
- Apply a log-transform to `charges` to correct its right-skew before modeling.
- Explore interaction terms, e.g., `is_smoker × bmi_category_Obese`, since
  smoking and obesity together tend to compound charge increases.
- Validate feature selection with train/test splits and cross-validation
  rather than relying solely on full-dataset correlation/chi-squared tests.
