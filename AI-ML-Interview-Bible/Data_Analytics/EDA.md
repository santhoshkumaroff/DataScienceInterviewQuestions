# Exploratory Data Analysis (EDA)

## What is it?
Exploratory Data Analysis (EDA) is the process of examining a dataset to understand its main characteristics. It involves data profiling, missing value analysis, outlier detection, correlation analysis, and univariate/bivariate visualizations. EDA guides feature engineering and model selection.

## Why do we use it?
EDA uncovers patterns, anomalies, and relationships before modeling. It prevents garbage-in-garbage-out. 80% of data science time is spent on EDA and data cleaning. A thorough EDA catches data quality issues, informs feature selection, and guides hypothesis testing.

## Real-Time Example 1
**Business Scenario:** A bank wants to build a customer churn prediction model. Before any modeling, the data team conducts EDA.
**Step-by-Step:**
1. Data profiling: `df.info()`, `df.describe()`, `df.shape`, `df.nunique()`.
2. Missing value analysis: `df.isnull().sum() / len(df) * 100` — find columns with >30% missing.
3. Univariate analysis: Histograms for numeric columns (Age, Balance, Transactions). Bar charts for categorical (Gender, Region, ProductType).
4. Bivariate analysis: Boxplot of Balance by Churn status. Bar chart of Region vs Churn rate.
5. Correlation analysis: Heatmap of numeric features. Identify multicollinearity (e.g., NumProducts and HasCreditCard).
6. Outlier detection: IQR method on TransactionAmount. Scatter plot of Age vs Balance.
7. Key findings: Churned customers have lower balances and fewer products. Certain regions have 2x churn rate.

## Real-Time Example 2
**Industry:** Healthcare
**Use Case:** Analyzing patient readmission data.
1. Load data: patient demographics, diagnoses, length of stay, previous admissions, readmitted flag.
2. Profile: Check for duplicate patient IDs, inconsistent date formats, impossible age values (>120).
3. Missing data: DiagnosisCode has 15% nulls — decide to impute or drop.
4. Target distribution: Readmission rate = 12% (imbalanced — important for model choice).
5. Numeric relationships: Length of stay vs readmission (boxplot). Older age correlates with readmission.
6. Categorical relationships: Admission type, insurance type, department — chi-square tests.
7. Temporal: Readmission by month — seasonal patterns.
8. Action: Create feature for previous admission count, flag for chronic conditions.

## Concept Example 1
**Univariate Analysis with Histogram and Boxplot**
- **Code:**
  ```python
  fig, axes = plt.subplots(1, 2, figsize=(12, 4))
  axes[0].hist(df['Age'], bins=30, edgecolor='black')
  axes[0].set_title('Age Distribution')
  axes[1].boxplot(df['Age'], vert=False)
  axes[1].set_title('Age Boxplot')
  ```
- **Output:** Histogram shows right-skewed age distribution (younger demographic). Boxplot shows outliers above 70.
- **Explanation:** Histogram shows full distribution shape. Boxplot shows median, quartiles, and outliers. Together they give a complete picture of a single variable.

## Concept Example 2
**Bivariate Analysis with Grouped Aggregation**
- **Code:**
  ```python
  churn_rate = df.groupby('ContractType')['Churned'].mean().reset_index()
  sns.barplot(data=churn_rate, x='ContractType', y='Churned')
  plt.title('Churn Rate by Contract Type')
  ```
- **Output:** Month-to-month contracts: 42% churn. 1-year: 11% churn. 2-year: 3% churn.
- **Explanation:** Grouped aggregation reveals how a categorical variable relates to the target. Bar plots make comparisons intuitive. This insight drives retention strategy.

## Common Mistakes
- Skipping EDA and jumping straight to modeling.
- Not handling missing values before visualization (skewed plots).
- Using correlation only on raw data without checking linearity.
- Ignoring outliers — blindly removing or keeping them.
- Not checking data types (dates as strings, numbers as objects).
- Analyzing without understanding business context (domain knowledge).

## How Interviewers Ask This
- "Walk me through your EDA process."
- "How do you detect and handle outliers?"
- "How do you analyze missing data and decide what to do?"
- "What's the difference between univariate, bivariate, and multivariate analysis?"

## How To Impress The Interviewer
- Mention automated EDA tools: `pandas_profiling` (ydata-profiling), `sweetviz`, `dtale`.
- Discuss that EDA should generate hypotheses, not just describe data.
- Use statistical tests (chi-square, t-test, ANOVA, KS test) alongside visual EDA.
- Show interaction effects — bivariate is not enough, check 3-way interactions.
- Document EDA findings in a structured report linking insights to business decisions.

## Most Asked Questions

**1. What is Exploratory Data Analysis (EDA)?**
EDA is the process of analyzing datasets to summarize their main characteristics using visual and statistical methods. It includes data profiling, distribution analysis, missing value analysis, outlier detection, correlation analysis, and hypothesis generation before formal modeling.

**2. What are the main steps in EDA?**
(1) Data profiling: shape, types, basic statistics. (2) Missing value analysis: percentage and patterns. (3) Univariate analysis: distributions of individual variables. (4) Bivariate analysis: relationships between two variables. (5) Multivariate analysis: interactions among three+ variables. (6) Outlier detection. (7) Correlation analysis.

**3. What is the difference between univariate, bivariate, and multivariate analysis?**
Univariate analyzes one variable (histogram, boxplot). Bivariate analyzes two variables (scatter plot, correlation, cross-tab). Multivariate analyzes three or more (pairplot, 3D scatter, partial correlation, interaction effects).

**4. How do you handle missing values?**
(1) Remove: drop rows/columns if missing >50% or random. (2) Impute numeric: mean (normal), median (skewed), KNN, regression. (3) Impute categorical: mode, "Unknown" category, predictive model. (4) Flag: create an "is_missing" indicator column. Always analyze missing patterns (MCAR, MAR, MNAR).

**5. What is MCAR, MAR, and MNAR?**
MCAR (Missing Completely At Random): no pattern — safe to drop or impute. MAR (Missing At Random): pattern depends on observed data — imputation works. MNAR (Missing Not At Random): pattern depends on missing value itself — needs careful modeling (Heckman, sensitivity analysis).

**6. How do you detect outliers?**
(1) IQR method: outliers < Q1 - 1.5×IQR or > Q3 + 1.5×IQR. (2) Z-score: |z| > 3. (3) Modified Z-score: |MAD| > 3.5 (robust to outliers). (4) DBSCAN clustering. (5) Isolation Forest. (6) Visual: boxplot, scatter plot, distribution plot.

**7. How do you handle outliers?**
(1) Remove if data entry error or measurement error. (2) Cap/Clip: winsorize at percentiles (1st, 99th). (3) Transform: log, square root, Box-Cox. (4) Treat separately: create a flag and model outliers independently. (5) Keep if they represent real phenomena (fraud, rare events).

**8. How do you perform correlation analysis?**
Pearson correlation for linear relationships (numeric-numeric). Spearman rank for monotonic relationships. Point-biserial for binary-numeric. Cramér's V for categorical-categorical. Visualize with heatmap, pairplot, scatter matrix.

**9. What is multicollinearity and why does it matter?**
Multicollinearity occurs when independent variables are highly correlated. It inflates standard errors in regression, makes coefficients unstable, and reduces interpretability. Detect with: correlation matrix (>0.8), VIF (Variance Inflation Factor > 5-10). Fix by removing or combining correlated features.

**10. How do you analyze categorical variables?**
(1) Frequency counts and bar plots. (2) Mode and value counts. (3) Cross-tabulation with target variable. (4) Chi-square test for independence. (5) Cramér's V for association strength. (6) Grouped bar plots with target proportions.

**11. What is data profiling?**
Data profiling involves checking: shape (rows, columns), column data types, unique values, missing values, basic statistics (mean, median, min, max, standard deviation), value ranges, and sample rows. Use `df.info()`, `df.describe()`, `df.head()`, `df.isnull().sum()`.

**12. How do you analyze the target variable distribution?**
Histogram or KDE for continuous target. Bar chart for categorical target. Check: skewness, class imbalance (percentage per class), outliers. For imbalanced targets (>10:1 ratio), plan for resampling or class weights.

**13. What is skewness and how do you handle it?**
Skewness measures asymmetry. Positive skew (long right tail): apply log, sqrt, Box-Cox, or Yeo-Johnson transform. Negative skew (long left tail): square, cube, or exponential transform. Target skew can affect regression performance.

**14. How do you handle date/time features?**
Extract: year, month, day, dayofweek, quarter, hour. Create cyclical encoding: sin/cos of month/hour for cyclic patterns. Create lag features, rolling windows. Calculate recency, frequency, and duration. Plot time series for trend/seasonality.

**15. What is the difference between training and test set EDA?**
EDA should be conducted on training data only to avoid data leakage. Test data should remain unseen. After EDA, apply the same transformations (from training) to test data. Check that test distribution is similar to training (distribution shift detection).

**16. How do you detect data leakage during EDA?**
(1) Check if target information appears in features (future data). (2) Check if data is shuffled before train/test split (temporal leakage). (3) Check if duplicates exist across train/test. (4) Look for features that perfectly predict the target (ID-like columns).

**17. What is the purpose of a pairplot in EDA?**
Pairplot visualizes pairwise relationships between numeric variables. It reveals: correlations, clusters, outliers, non-linear relationships, and separation by categories. Helps decide which features are useful for modeling and which are redundant.

**18. How do you create an EDA report?**
Tools: (1) `pandas_profiling` (now ydata-profiling) generates auto reports. (2) `sweetviz` compares datasets. (3) `dtale` interactive exploration. (4) Custom: markdown with visualizations using matplotlib/seaborn. Structure: overview, missing data, univariate, bivariate, correlation, key findings.

**19. What is the difference between exploratory and confirmatory analysis?**
Exploratory (EDA): hypothesis generation, pattern discovery, no pre-defined tests. Confirmatory (CDA): hypothesis testing, statistical inference, pre-registered analyses. EDA is flexible and visual. CDA is rigorous and statistical. Both are needed.

**20. How do you check for data quality issues?**
(1) Duplicate rows. (2) Inconsistent formatting (NY, N.Y., New York). (3) Domain violations (negative age, future birth dates). (4) Invalid values (5-digit phone numbers). (5) Outliers. (6) Data type mismatches. (7) Referential integrity (foreign key values missing).

**21. What is feature engineering during EDA?**
Creating new features based on EDA insights: interaction terms (Age × Income), aggregates (avg purchase per customer), binning (age groups), encoding (one-hot, target encoding), text features (length, word count), and domain-specific features (BMI from height/weight).

**22. How do you analyze high-cardinality categorical variables?**
(1) Frequency encoding (replace category with count). (2) Target encoding (mean target per category). (3) Group rare categories into "Other". (4) Use category embeddings for very high cardinality. (5) Hash encoding. Visualize top 20 categories, group the rest.

**23. What is the Kolmogorov-Smirnov (KS) test in EDA?**
KS test compares two distributions to see if they're significantly different. Used in EDA to: compare train vs test distributions, compare distributions across segments, and assess feature importance (distribution of feature for target=0 vs target=1).

**24. How do you analyze imbalanced datasets?**
(1) Check class proportions. (2) Use stratified sampling. (3) Visualize with bar chart and KDE per class. (4) Consider: resampling (SMOTE, ADASYN, RandomUnderSampler), class weights, anomaly detection approaches, or specialized metrics (precision-recall, F1, AUC).

**25. What are the common visualization types for EDA?**
Numeric single: histogram, boxplot, KDE. Categorical single: bar chart, pie (use sparingly). Numeric-Numeric: scatter plot, hexbin, pairplot, correlation heatmap. Categorical-Numeric: boxplot, violinplot, barplot with error bars. Categorical-Categorical: stacked bar, heatmap (counts), mosaic plot.

**26. How do you use statistics in EDA?**
(1) Central tendency: mean, median, mode. (2) Dispersion: range, IQR, variance, std. (3) Shape: skewness, kurtosis. (4) Association: Pearson/Spearman correlation, chi-square, mutual information. (5) Tests: t-test, ANOVA, KS test, Shapiro-Wilk (normality).

**27. What is the purpose of a Q-Q plot?**
Q-Q plot compares data quantiles to theoretical normal distribution quantiles. If points fall along the diagonal line, the data is approximately normal. Deviations show skewness or heavy tails. Used to check normality assumption before parametric tests.

**28. How do you detect seasonality and trends in time series during EDA?**
(1) Line plot of time series. (2) Decomposition (additive/multiplicative): trend, seasonal, residual. (3) Autocorrelation plot (ACF). (4) Rolling statistics (moving average, rolling std). (5) Seasonal subseries plots (values per month across years).

**29. What is the curse of dimensionality and how does EDA help?**
As the number of features grows, data becomes sparse, making distances and density unreliable. EDA helps by: identifying redundant features (correlation), removing low-variance features, detecting irrelevant features (low target correlation), and guiding dimensionality reduction (PCA, t-SNE).

**30. How do you present EDA findings to stakeholders?**
(1) Start with key findings (not process). (2) Use clear, simple visuals with annotations. (3) Connect insights to business impact (e.g., "10% of customers contribute 50% of revenue"). (4) Avoid technical jargon. (5) Provide actionable recommendations. (6) Include a 1-page executive summary.
