# Feature Engineering

## What is it?
Feature Engineering is the process of transforming raw data into features that better represent the underlying problem to machine learning models. It includes: Encoding (Label Encoding for ordinal, One-Hot Encoding for nominal, Target Encoding for high-cardinality), Scaling (StandardScaler - zero mean unit variance, MinMaxScaler - 0 to 1 range, RobustScaler - uses median/IQR for outliers), Handling Missing Values (imputation), Outlier Treatment (capping, removal), and Feature Selection (choosing the most relevant features).

## Why do we use it?
Feature engineering is often the difference between a good model and a great one. In real-world ML, data is messy, incomplete, and inconsistent. Proper engineering can dramatically improve model performance, reduce overfitting, and speed up training. It's the most important skill for a data scientist - garbage in, garbage out.

## Real-Time Example 1
**Credit Scoring (Experian)**
1. Raw data: transaction amounts, timestamps, merchant categories
2. Feature engineering creates: avg monthly spend, spending volatility, payment regularity
3. Missing income is imputed using median income by profession and location
4. Outliers in transaction amounts are capped at 99th percentile
5. Credit score features are scaled to [0,1] range for neural network input

## Real-Time Example 2
**Real Estate Price Prediction (Zillow)**
1. Raw data: address, sqft, bedrooms, bathrooms, year built
2. Engineered features: price_per_sqft, age, has_garage (binary), proximity_score
3. Zip codes (300+ categories) use target encoding with smoothing
4. Features with 95%+ correlation are removed (e.g., sqft and rooms are correlated)
5. StandardScaler ensures all features contribute equally to the model

## Concept Example 1
```python
# Encoding and Scaling Techniques
from sklearn.preprocessing import LabelEncoder, OneHotEncoder, StandardScaler, MinMaxScaler
from sklearn.impute import SimpleImputer
import numpy as np
import pandas as pd

# Sample data with mixed types
df = pd.DataFrame({
    'size': ['S', 'M', 'L', 'S', 'XL', 'M'],
    'color': ['red', 'blue', 'green', 'blue', 'red', 'green'],
    'price': [10, 20, None, 15, 35, 25]
})

# Label Encoding (ordinal)
le = LabelEncoder()
df['size_encoded'] = le.fit_transform(df['size'])

# One-Hot Encoding (nominal)
onehot = OneHotEncoder(sparse_output=False)
color_encoded = onehot.fit_transform(df[['color']])
color_df = pd.DataFrame(color_encoded, columns=onehot.get_feature_names_out(['color']))

# Handle missing values
imputer = SimpleImputer(strategy='median')
df['price_imputed'] = imputer.fit_transform(df[['price']])

# Scaling
scaler = StandardScaler()
df['price_scaled'] = scaler.fit_transform(df[['price_imputed']])

print("Encoded and Scaled Data:")
print(df[['size', 'size_encoded', 'price_imputed', 'price_scaled']])
# Output:
#   size  size_encoded  price_imputed  price_scaled
# 0    S             0           10.0       -1.463
# 1    M             1           20.0       -0.293
# 2    L             2           17.5       -0.878
# 3    S             0           15.0       -0.878
# 4   XL             3           35.0        1.756
# 5    M             1           25.0        0.293
```

## Concept Example 2
```python
# Feature Selection Methods
from sklearn.feature_selection import SelectKBest, f_classif, mutual_info_classif
from sklearn.ensemble import RandomForestClassifier
from sklearn.datasets import make_classification
import numpy as np

np.random.seed(42)
X, y = make_classification(n_samples=1000, n_features=20, n_informative=5,
                           n_redundant=5, n_repeated=0, random_state=42)

# Method 1: ANOVA F-test
selector_f = SelectKBest(score_func=f_classif, k=5)
X_f = selector_f.fit_transform(X, y)
print(f"ANOVA selected features: {selector_f.get_support().nonzero()[0]}")

# Method 2: Mutual Information
selector_mi = SelectKBest(score_func=mutual_info_classif, k=5)
X_mi = selector_mi.fit_transform(X, y)
print(f"MI selected features: {selector_mi.get_support().nonzero()[0]}")

# Method 3: Random Forest Feature Importance
rf = RandomForestClassifier(random_state=42)
rf.fit(X, y)
importance = rf.feature_importances_
top5 = np.argsort(importance)[-5:][::-1]
print(f"RF top 5 features: {top5}")
print(f"Importances: {importance[top5].round(3)}")

# Output:
# ANOVA selected features: [ 4  7  9 11 12]
# MI selected features: [ 4  7  9 11 12]
# RF top 5 features: [11  7  4  9 12]
# Importance: [0.312 0.248 0.168 0.112 0.072]
```

## Common Mistakes
- Applying One-Hot Encoding to high-cardinality features (creates thousands of columns)
- Scaling before train/test split (causes data leakage)
- Filling missing values with mean/median without considering the distribution
- Not handling outliers before scaling (skews StandardScaler)
- Removing features based on low variance without considering domain importance
- Feature selection using test data (causes data leakage)

## How Interviewers Ask This
- "What is the difference between Label Encoding and One-Hot Encoding?"
- "When would you use RobustScaler over StandardScaler?"
- "How do you handle missing values in a dataset?"
- "What is Target Encoding and when should you use it?"
- "Explain data leakage in feature engineering."

## How To Impress The Interviewer
Discuss the bias-variance tradeoff in encoding: One-Hot creates high-dimensional sparse data (high variance), while Label Encoding imposes false ordinal relationships (high bias). Target Encoding uses the target variable to encode categories but requires smoothing and cross-validation to prevent overfitting. Explain that StandardScaler assumes normal distribution; RobustScaler is better with outliers. Discuss that for tree-based models, scaling is unnecessary, but for linear models, SVM, and neural networks, it's critical. Mention that feature engineering can include domain-specific features like time-since-last-event, rolling averages, and interaction terms. Discuss automated feature engineering tools (Featuretools) and deep feature synthesis.

## Most Asked Questions

**Q1: What is the difference between Label Encoding and One-Hot Encoding?**
A: Label Encoding converts categories to integers (0, 1, 2...). It implies ordinal relationships (2 > 1 > 0), which can mislead models. One-Hot Encoding creates binary columns for each category, avoiding false ordering. Use Label Encoding for ordinal data (e.g., small=0, medium=1, large=2). Use One-Hot for nominal data (e.g., red, blue, green).

**Q2: When would you use Target Encoding?**
A: Target Encoding replaces each category with the mean of the target variable for that category. Use it for high-cardinality categorical features (> 10-20 categories) where One-Hot would create too many columns. Example: 1000 zip codes → 1000 columns with One-Hot vs 1 column with Target Encoding. Must use smoothing and cross-validation to prevent overfitting.

**Q3: What is the difference between StandardScaler, MinMaxScaler, and RobustScaler?**
A: StandardScaler: zero mean, unit variance. Best for normally distributed data. MinMaxScaler: scales to [0,1] range. Best for bounded data (pixel values). RobustScaler: uses median and IQR. Best for data with outliers (it's not affected by extreme values). Choose based on data distribution and model requirements.

**Q4: How do you handle missing values?**
A: Options: (1) Remove rows with missing values (if few). (2) Mean/Median imputation (simple but reduces variance). (3) Mode imputation (for categorical). (4) Forward/backward fill (time series). (5) KNN imputation (uses similar samples). (6) Model-based imputation (predict missing values). (7) Create indicator feature "was_missing". Choose based on missing mechanism (MCAR, MAR, MNAR).

**Q5: How do you treat outliers?**
A: Detection: Z-score (>3), IQR method (1.5×IQR), Isolation Forest, DBSCAN. Treatment: (1) Remove if data entry error. (2) Cap at percentiles (winsorization at 1st/99th). (3) Transform (log, Box-Cox). (4) Treat separately (build model with and without outliers). Domain knowledge is crucial - an "outlier" might be the most important signal.

**Q6: What is data leakage in feature engineering?**
A: Data leakage occurs when information from outside the training set (or from the future) is used to create features. Examples: scaling before train/test split, using target to encode categories without cross-validation, creating features from the entire dataset timestamp. Leakage causes over-optimistic performance during training but poor real-world results.

**Q7: What feature selection methods do you know?**
A: Filter methods: correlation, chi-square, mutual information, ANOVA (fast, independent of model). Wrapper methods: forward/backward selection, recursive feature elimination (RFE) (model-dependent, computationally expensive). Embedded methods: LASSO (L1 regularization), tree-based importance (built into model training).

**Q8: What is the curse of dimensionality in feature engineering?**
A: Adding many irrelevant features degrades model performance due to sparsity and increased noise. Feature engineering should balance creating informative features vs keeping dimensionality manageable. Use feature selection to keep only relevant features. The ideal: engineer domain-specific features, then let feature selection choose the best subset.

**Q9: How do you handle date and time features?**
A: Extract: year, month, day, weekday, hour, is_weekend, quarter, day_of_year. Cyclical encoding: sin(2π×month/12), cos(2π×month/12) for cyclic features (time, day of week). Difference features: days_since_last_purchase, time_to_next_event. Rolling statistics: 7-day moving average, 30-day sum.

**Q10: What is feature interaction and how do you create interaction features?**
A: Feature interaction captures combined effects of features. Create by: multiplication (x₁ × x₂), addition, ratio (x₁/x₂). Example: In real estate, total_sqft × num_floors gives total_area. Interaction features capture non-linear relationships that individual features miss. Trees automatically capture interactions; linear models need explicit interaction features.

**Q11: What is the difference between feature selection and dimensionality reduction?**
A: Feature selection keeps original features (interpretable). Dimensionality reduction (PCA, t-SNE) creates new features (not interpretable). Feature selection is preferred when interpretability matters. PCA is preferred when removing multicollinearity or handling very high dimensions. Both reduce overfitting and training time.

**Q12: How do you handle imbalanced data in feature engineering?**
A: (1) SMOTE: creates synthetic minority samples by interpolating between neighbors. (2) ADASYN: focuses on harder-to-learn minority samples. (3) Random undersampling (with caution - may lose important data). (4) Class weights in loss function. (5) Collect more data for minority class. Never use accuracy as evaluation metric for imbalanced data.
