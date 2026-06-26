# Data Analyst Interview Questions

## What is it?
This file compiles 35+ data analyst interview questions covering SQL, Python (Pandas, NumPy, Matplotlib, Seaborn), statistics, Excel, data cleaning, EDA, business acumen, scenario-based problems, and behavioral questions. These are the most asked questions in data analyst interviews.

## Why do we use it?
Data analyst interviews test both technical skills and business thinking. You need to write code, interpret results, communicate insights, and solve ambiguous business problems. This guide covers the full spectrum of topics you'll face.

## Real-Time Example 1
**Interview Scenario:** The interviewer gives you a CSV of 100,000 online store transactions and asks: "What is the revenue trend and which products drive growth?"
**Solution:**
1. `df = pd.read_csv('transactions.csv', parse_dates=['Date'])`.
2. `df['Month'] = df['Date'].dt.to_period('M')`.
3. `monthly_rev = df.groupby('Month')['Revenue'].sum()`.
4. `monthly_rev.pct_change()` — growth rate.
5. `df.groupby('Product')['Revenue'].sum().nlargest(10).plot(kind='bar')`.
6. Answer: Revenue is up 15% this quarter; Electronics and Home categories drive 70% of growth.

## Real-Time Example 2
**Interview Scenario:** A/B test results are provided: control and treatment group conversion rates. Which is significant?
**Solution:**
1. Calculate conversion: control = 120/5000 = 2.4%, treatment = 180/5000 = 3.6%.
2. Run two-proportion z-test: `from statsmodels.stats.proportion import proportions_ztest`.
3. `z, p = proportions_ztest([120, 180], [5000, 5000])`.
4. p-value = 0.0003 (< 0.05) — statistically significant.
5. Business recommendation: implement the treatment (3.6% vs 2.4% = 50% relative improvement).

## Most Asked Questions

**1. What is the difference between structured and unstructured data?**
Structured data is organized in rows and columns (SQL tables, Excel sheets). Unstructured data has no predefined format (images, text, audio, video). Semi-structured (JSON, XML) has tags but flexible schema. Data analysts primarily work with structured and semi-structured data.

**2. Explain the difference between data cleaning, data transformation, and data modeling.**
Cleaning: fixing errors, handling missing values, removing duplicates. Transformation: reshaping, aggregating, creating new features. Modeling: defining relationships between tables (star schema) for efficient querying. In practice, they overlap in the data pipeline.

**3. How do you handle missing values in a dataset?**
Check percentage missing. Options: (1) Drop if >50% missing or random. (2) Impute with mean/median (numeric) or mode (categorical). (3) Use forward fill for time series. (4) Create indicator column for missingness. (5) Use ML imputation (KNN, MICE). Context matters.

**4. What is the difference between correlation and causation?**
Correlation means two variables move together (positive or negative). Causation means one directly affects the other. Correlation ≠ causation due to confounders, reverse causality, or spurious relationships. Example: Ice cream sales and drowning are correlated (both increase in summer — confounder = temperature).

**5. What is a p-value and how do you interpret it?**
A p-value is the probability of observing results as extreme as the data, assuming the null hypothesis is true. p < 0.05 typically means statistically significant (reject null). p > 0.05 means insufficient evidence to reject null. Not the probability that the null is true.

**6. What is the difference between Type I and Type II error?**
Type I (False Positive): rejecting a true null hypothesis (false alarm). Type II (False Negative): failing to reject a false null hypothesis (miss). α controls Type I, β controls Type II. Power = 1 - β. Trade-off between them.

**7. Explain the Central Limit Theorem.**
The sampling distribution of the sample mean approaches a normal distribution as sample size increases (n ≥ 30), regardless of the population's distribution. This enables hypothesis testing and confidence intervals even when the population is not normal.

**8. What is the difference between a left join, inner join, and full outer join?**
Inner join: only matching rows from both tables. Left join: all rows from left table, matched from right (nulls if no match). Full outer: all rows from both tables (nulls where no match). Used in both SQL and Pandas merge.

**9. How would you detect outliers in a dataset?**
Statistical: Z-score (|z| > 3), IQR method (outside Q1-1.5×IQR or Q3+1.5×IQR). Visual: boxplot, histogram, scatter plot. ML: Isolation Forest, DBSCAN. Domain knowledge is critical — an outlier might be real (fraud transaction, rare disease).

**10. What is the difference between variance and standard deviation?**
Variance measures the average squared deviation from the mean. Standard deviation is the square root of variance (same units as data). Std dev is more interpretable. Example: if salaries have σ = $15,000, that's easier to understand than σ² = 225,000,000.

**11. How do you perform A/B testing analysis?**
(1) Define hypothesis (H0: no difference, H1: difference). (2) Collect data with random assignment. (3) Choose test (t-test for means, z-test for proportions, chi-square for categorical). (4) Calculate p-value and confidence interval. (5) Consider practical significance (effect size), not just statistical.

**12. What is Simpson's Paradox?**
A trend appears in several groups but disappears or reverses when the groups are combined. Caused by confounding variables or unequal group sizes. Example: UC Berkeley gender bias case — each department admitted women at similar rates, but overall showed bias because women applied to competitive departments.

**13. How do you decide which visualization to use?**
(1) Trend over time → line chart. (2) Comparison across categories → bar chart. (3) Distribution → histogram or boxplot. (4) Relationship between two numerics → scatter plot. (5) Composition → stacked bar or treemap (avoid pie charts with >5 categories). (6) Correlation matrix → heatmap.

**14. What is the difference between mean, median, and mode?**
Mean: arithmetic average (sensitive to outliers). Median: middle value (robust to outliers). Mode: most frequent value (used for categorical data). For income data, median is better because billionaires skew the mean upward.

**15. How do you explain a technical finding to a non-technical stakeholder?**
(1) Start with the business impact ("We can increase revenue by 15%"). (2) Use simple analogies. (3) Visualize (a chart is worth 1000 p-values). (4) Avoid jargon (p-value → "how confident we are"). (5) Provide 2-3 clear recommendations.

**16. What is SQL window functions and give an example?**
Window functions perform calculations across rows related to the current row without collapsing them. Example: `RANK() OVER (PARTITION BY Department ORDER BY Salary DESC)` ranks employees within each department. Others: ROW_NUMBER, LAG, LEAD, SUM() OVER (ORDER BY Date).

**17. How do you handle imbalanced datasets?**
(1) Resampling: oversample minority (SMOTE), undersample majority. (2) Class weights: penalize misclassification of minority more. (3) Use appropriate metrics: precision, recall, F1, AUC-ROC instead of accuracy. (4) Anomaly detection approach for extreme imbalance.

**18. What is the difference between long and wide data format?**
Wide: each subject has one row, multiple columns for measurements (Excel-style). Long: each measurement is a separate row (tidy data). Long format is required for most visualization libraries (Seaborn, ggplot) and for many ML algorithms.

**19. How do you perform feature selection?**
(1) Filter methods: correlation, mutual information, chi-square, variance threshold. (2) Wrapper methods: forward/backward selection, RFE. (3) Embedded methods: Lasso (L1 regularization), tree-based importance (Random Forest, XGBoost). (4) Domain knowledge: remove irrelevant features.

**20. What is the difference between one-hot encoding and label encoding?**
One-hot: creates binary columns for each category (n columns for n categories). Label: assigns integer 0, 1, 2... (1 column). One-hot doesn't imply order; label encoding implies order (0 < 1 < 2). Use one-hot for nominal categories, label for ordinal.

**21. Explain what cross-validation is.**
Cross-validation splits data into k folds, trains on k-1 folds and validates on the remaining fold, repeating k times. K-fold (k=5 or 10) is standard. Stratified CV preserves class proportions. Time series CV respects temporal order. Prevents overfitting and gives robust performance estimates.

**22. What is the difference between bagging and boosting?**
Bagging (Random Forest): train models independently in parallel, average predictions (reduces variance). Boosting (XGBoost, LightGBM): train models sequentially, each correcting previous errors (reduces bias). Bagging handles noisy data better; boosting often performs better on clean data.

**23. How do you handle data leakage?**
Data leakage occurs when information from the future or outside the training set influences the model. Prevention: (1) Split data before any processing. (2) Fit scalers/encoders on train only. (3) No target information in features. (4) Time-based split for temporal data. (5) No duplicates across train/test.

**24. What is the difference between regression and classification?**
Regression predicts a continuous numeric value (price, temperature, revenue). Classification predicts a discrete class/category (spam/not spam, churn yes/no, digit 0-9). Evaluation differs: RMSE/MAE for regression, accuracy/precision/recall/F1 for classification.

**25. How do you measure the performance of a regression model?**
R-squared (proportion of variance explained), Adjusted R-squared (penalizes features), RMSE (root mean squared error — in original units), MAE (mean absolute error), MAPE (percentage error). Always compare to a baseline (mean prediction).

**26. How do you measure the performance of a classification model?**
Confusion matrix → accuracy, precision, recall, F1-score. ROC-AUC (area under curve — 0.5=random, 1.0=perfect). Precision-Recall curve for imbalanced data. Log loss (probability calibration). Business metrics (cost of false positives vs false negatives).

**27. What is the difference between supervised and unsupervised learning?**
Supervised: labeled data (X → y). Examples: regression, classification, time series forecasting. Unsupervised: no labels, finding patterns. Examples: clustering (K-means), dimensionality reduction (PCA), anomaly detection, association rules.

**28. Explain the bias-variance tradeoff.**
High bias: model is too simple (underfitting) — misses patterns. High variance: model is too complex (overfitting) — captures noise. Goal: find the sweet spot. Regularization, cross-validation, and ensemble methods manage this tradeoff.

**29. How do you handle large datasets that don't fit in memory?**
(1) Load in chunks (pandas `chunksize`). (2) Use out-of-core tools: Dask, Vaex, PySpark. (3) Optimize dtypes (int8, float32, categorical). (4) Sample data for prototyping. (5) Push computation to database (SQL aggregation before loading).

**30. What is a data pipeline?**
A data pipeline automates the flow of data from source to destination with transformations in between. Stages: Extract (from APIs, databases, files), Transform (clean, aggregate, join), Load (to data warehouse, data lake). Tools: Airflow, Prefect, dbt, Luigi.

**31. Tell me about a time you used data to solve a business problem.**
Use the STAR method: Situation (sales declining 10%), Task (identify root cause), Action (analyzed product categories, regions, customer segments; built dashboard), Result (found 3 underperforming products, recommended discontinuation, restored profit margins).

**32. How would you approach a problem with incomplete requirements?**
(1) Clarify the business objective (what decision will this inform?). (2) Ask about available data. (3) Propose a quick prototype/exploratory analysis. (4) Iterate with stakeholder feedback. (5) Document assumptions and limitations.

**33. How do you stay current with data analytics tools and trends?**
(1) Follow industry blogs/communities (Towards Data Science, KDnuggets). (2) Take online courses (Coursera, DataCamp). (3) Personal projects / Kaggle competitions. (4) Attend meetups/conferences. (5) Read documentation and release notes.

**34. What is your favorite SQL function and why?**
Choose one and explain: RANK()/DENSE_RANK() for window ranking, LAG() for previous row comparison, or STRING_AGG for concatenation. Example: LAG() is excellent for calculating month-over-month changes without self-joins.

**35. How do you prioritize multiple data requests from stakeholders?**
(1) Understand impact vs effort (high impact, low effort first). (2) Align with business goals and deadlines. (3) Negotiate scope — can a quick analysis answer 80% of the question? (4) Communicate trade-offs honestly. (5) Build self-service dashboards to reduce ad-hoc requests.

**36. What tools do you use for creating dashboards and reports?**
Mention 2-3: Tableau/Power BI (interactive dashboards), Looker (embedded analytics), Metabase (open source), Google Data Studio. Explain when you'd choose each: Power BI for enterprise, Tableau for visual exploration, Metabase for quick internal views.

**37. How do you ensure data quality in your analyses?**
(1) Profile data at each stage (row counts, null checks, distribution checks). (2) Set up automated tests (Great Expectations, dbt tests). (3) Cross-validate with source systems. (4) Peer review code. (5) Document known issues and limitations.

**38. Explain the concept of stationarity in time series.**
A stationary time series has constant mean, variance, and autocorrelation over time. Non-stationary (trend, seasonality) needs differencing or detrending before modeling. Check with: rolling statistics, ADF test, KPSS test.

**39. How do you perform text analysis as a data analyst?**
(1) Text cleaning (lowercase, remove punctuation/stopwords, stemming/lemmatization). (2) Feature extraction: bag-of-words, TF-IDF, n-grams. (3) Sentiment analysis (VADER, TextBlob). (4) Topic modeling (LDA). (5) Word clouds, bar charts of top terms.

**40. What is your approach to learning a new tool or technology?**
(1) Start with official documentation and tutorials. (2) Build a small personal project end-to-end. (3) Solve real problems (current work tasks). (4) Read community resources (Stack Overflow, Medium). (5) Teach someone else — solidifies learning.
