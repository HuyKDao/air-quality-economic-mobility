# Air Quality and Economic Mobility in California

**Does growing up with dirtier air hold kids back economically?** This group research project for **Data C102, Data, Inference and Decisions** (UC Berkeley, Fall 2024) uses causal inference and predictive modeling to study whether childhood exposure to fine particulate matter (PM2.5) affects adult income in California counties.

**Team:** Lan Dinh, Faith Dennis, Huy Dao, Anand Shankar. 📄 [Read the final report](report/final_report.pdf)

## Research questions
1. **Causal:** does poor county air quality *cause* lower upward mobility for the children who grow up there?
2. **Predictive:** can a county's PM2.5 levels across childhood (ages 8–18) predict those children's income percentile at age 27?

## Data
| Source | What it gives us |
|---|---|
| [CDC National Environmental Public Health Tracking Network](https://ephtracking.cdc.gov/) | Daily county-level PM2.5 estimates, 2001–2014 |
| [Opportunity Atlas](https://opportunityinsights.org/data/) (Opportunity Insights and U.S. Census Bureau) | County outcomes for the 1992 birth cohort by parental income percentile, plus county covariates |

Preprocessing narrowed the data to California counties, the 1992 cohort and pooled (all races and genders) estimates, and rolled daily PM2.5 up to yearly county means. The processed tables are in [`data/processed/`](data/processed/).

## Methods
- **Causal inference:**
  - Treatment: air quality binarized at the EPA's 12 µg/m³ PM2.5 standard (2001, when the cohort was 8–9).
  - Confounders: four 2000 county characteristics chosen by comparing their distributions across treatment groups (share of college graduates, foreign-born share, per-capita income, poverty rate).
  - Estimation: **propensity scores from logistic regression, with inverse propensity weighting** to estimate the average treatment effect (ATE). Cross-checked with **outcome regression** and **bootstrapped confidence intervals**.
- **Prediction:** yearly PM2.5 levels (2001–2010) as features and the income percentile at 27 as the target. We fit separate models per parental income percentile (1st, 25th, 50th, 75th, 100th) and compared:
  - Linear regression and **Gaussian GLMs**.
  - **Decision trees** and **random forests**.
  - A naive mean baseline.

## Results
- **Causal:** IPW estimates showed a **weak negative effect** of poor air quality on upward mobility in every parental income group (magnitudes of about 0.11–0.19). Bootstrapped outcome-regression ATEs were centered near zero, so the evidence is suggestive but not conclusive.
- **Prediction:** random forests worked best for most income groups (5-fold CV RMSE ≈ **0.021** percentile points). Compared with the naive baseline, **RMSE dropped by 23–71%** depending on the group, so air quality has real predictive value. Predictive power was weakest for children of the highest earners.
- **Limitations:** county-level aggregation hides neighborhood variation, there are few confounders, and there is only one cohort. Finer-grained data and more outcome measures are the natural next steps.

## My contribution
- Visualized and analyzed the relationship between air quality and youth mobility outcomes, and ranked the correlations of the candidate variables.
- Worked on the propensity score weighting used to adjust for confounders.
- Wrote the **Data Overview** section of the report and folded its findings into the conclusion.

## Repository layout
```
report/final_report.pdf                       # final paper
notebooks/01_data_preprocessing.ipynb         # Opportunity Atlas trends and covariates → California tables
notebooks/02_air_quality_preprocessing.ipynb  # CDC PM2.5 → yearly county means
notebooks/03_air_quality_eda.ipynb            # PM2.5 distributions and maps
notebooks/04_mobility_outcomes_eda.ipynb      # outcome distributions by parental income
notebooks/05_combined_eda.ipynb               # air quality vs. outcomes and covariates
data/processed/                               # cleaned California tables used in the analysis
```
The raw source files (over 200 MB) aren't included. Download them from the CDC and Opportunity Insights links above.

## Skills and tools
Python, pandas, NumPy, statsmodels (GLMs), scikit-learn (logistic regression, decision trees, random forests, cross-validation), causal inference (propensity scores, IPW, outcome regression, bootstrapping), geospatial visualization, Matplotlib, Seaborn, technical writing.
