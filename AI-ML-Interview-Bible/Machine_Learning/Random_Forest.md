# Random Forest

## What is it?
Random Forest is an ensemble of Decision Trees trained on random subsets of data (Bagging) and random subsets of features. It combines multiple weak learners into a strong learner by averaging their predictions. Key concepts: Bagging (Bootstrap Aggregating - training trees on bootstrap samples), Ensemble Learning (combining multiple models), Feature Importance (measuring how much each feature reduces impurity), and Out-of-Bag (OOB) Error (testing trees on data they weren't trained on).

## Why do we use it?
Random Forest is one of the most versatile and reliable algorithms. It handles high-dimensional data, non-linear relationships, and missing values well. No feature scaling needed. Used in: fraud detection (PayPal), customer churn prediction, medical diagnosis, stock market prediction. It often achieves state-of-the-art performance with minimal hyperparameter tuning.

## Real-Time Example 1
**Fraud Detection (PayPal)**
1. PayPal trains 500 decision trees on random subsets of transaction data
2. Each tree votes: "Fraud" or "Not Fraud"
3. Trees use different features (amount, location, device, velocity, IP)
4. Random forest reduces false positives compared to individual trees
5. OOB error provides unbiased performance estimate without a separate test set

## Real-Time Example 2
**Medical Diagnosis (Mayo Clinic)**
1. Patient data includes 500+ features (lab results, vitals, demographics)
2. Random Forest identifies top 10 features for diabetes diagnosis
3. Feature importance reveals: blood sugar level (0.35), BMI (0.15), age (0.10)
4. The ensemble of 1000 trees outperforms any single decision tree
5. Doctors use both the diagnosis and the feature importance for treatment decisions

## Concept Example 1
```python
# Random Forest vs Single Decision Tree
from sklearn.ensemble import RandomForestClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import numpy as np

np.random.seed(42)
X = np.random.randn(500, 10)
y = (X[:, 0] + X[:, 1]**2 + X[:, 2] > 0).astype(int)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# Single tree
tree = DecisionTreeClassifier(max_depth=10, random_state=42)
tree.fit(X_train, y_train)
tree_test_acc = accuracy_score(y_test, tree.predict(X_test))

# Random Forest
rf = RandomForestClassifier(n_estimators=100, max_depth=10, random_state=42)
rf.fit(X_train, y_train)
rf_test_acc = accuracy_score(y_test, rf.predict(X_test))

print(f"Decision Tree Test Accuracy: {tree_test_acc:.3f}")
print(f"Random Forest Test Accuracy: {rf_test_acc:.3f}")
print(f"Feature Importances: {rf.feature_importances_.round(3)}")
# Output:
# Decision Tree Test Accuracy: 0.850
# Random Forest Test Accuracy: 0.910
# Feature Importances: [0.352 0.261 0.103 0.046 0.038 ...]
# Explanation: Random Forest reduces variance and improves accuracy
```

## Concept Example 2
```python
# OOB Error and Number of Trees
from sklearn.ensemble import RandomForestClassifier
from sklearn.datasets import make_classification
import numpy as np

X, y = make_classification(n_samples=1000, n_features=10, random_state=42)

# OOB score gives unbiased estimate without test split
for n_trees in [10, 50, 100, 200, 500]:
    rf = RandomForestClassifier(
        n_estimators=n_trees,
        oob_score=True,
        random_state=42
    )
    rf.fit(X, y)
    print(f"Trees={n_trees:3d}: OOB Score = {rf.oob_score_:.4f}")

# Output:
# Trees= 10: OOB Score = 0.9370
# Trees= 50: OOB Score = 0.9520
# Trees=100: OOB Score = 0.9580
# Trees=200: OOB Score = 0.9560
# Trees=500: OOB Score = 0.9570
# Explanation: More trees improve performance up to a plateau (~100 trees)
```

## Common Mistakes
- Using too few trees (less than 100) leaving performance on the table
- Not using OOB score as a free validation metric
- Setting max_features too high (reduces tree diversity, hurts ensemble)
- Ignoring feature importance analysis for insights
- Using Random Forest on sparse high-dimensional data (use gradient boosting instead)
- Not tuning min_samples_leaf (controls overfitting)

## How Interviewers Ask This
- "How does Random Forest differ from a single Decision Tree?"
- "Explain Bagging and why it works."
- "What is Out-of-Bag error?"
- "How does Random Forest compute feature importance?"
- "Why does Random Forest not overfit as badly as a single tree?"

## How To Impress The Interviewer
Explain the mathematical intuition: Bagging reduces variance without increasing bias. For a single tree with variance σ², the average of m independent trees has variance σ²/m. However, trees are correlated (trained on similar data), so the actual reduction is less. Random feature selection further decorrelates trees. Discuss the "double randomness" principle: each tree sees random bootstrap samples AND random feature subsets. Mention that Random Forest is one of the few algorithms that works well "out of the box" without much tuning. Talk about Extremely Randomized Trees (ExtraTrees) which adds more randomness by randomizing split thresholds.

## Most Asked Questions

**Q1: How does Random Forest work?**
A: (1) Create m bootstrap samples from training data (sampling with replacement). (2) Train a decision tree on each sample, considering random subset of features at each split. (3) For classification: majority vote. For regression: average predictions. The randomness makes trees diverse, and averaging reduces variance.

**Q2: What is Bagging (Bootstrap Aggregating)?**
A: Bagging creates multiple training subsets by sampling with replacement (bootstrap). Each model trains on a different subset. Predictions are averaged. This reduces variance while keeping bias unchanged. Random Forest extends Bagging by also randomly selecting features at each split.

**Q3: What is Out-of-Bag (OOB) Error?**
A: Each bootstrap sample leaves out ~37% of training data (the "out-of-bag" samples). These samples can validate each tree. The OOB score is the average error across all trees on their OOB samples. It provides an unbiased performance estimate without a separate test set, similar to cross-validation.

**Q4: How does Random Forest compute feature importance?**
A: Feature importance measures how much each feature reduces impurity (Gini or MSE) across all trees. Features that appear high in the tree and split pure nodes get higher importance. It's calculated by averaging the impurity decrease over all trees, normalized to sum to 1. Warning: correlated features share importance.

**Q5: Why doesn't Random Forest overfit as much as a single tree?**
A: Averaging many diverse trees reduces variance. While individual trees may overfit their bootstrap samples, the ensemble smooths out overfitting. Adding more trees never hurts (up to a point - it just becomes computationally expensive). The law of large numbers ensures convergence.

**Q6: What are the key hyperparameters of Random Forest?**
A: n_estimators (number of trees - higher is better but slower), max_depth (tree depth), min_samples_leaf (controls overfitting), max_features (sqrt(n) for classification, n/3 for regression is default), max_samples (bootstrap sample size). Tune these to balance bias-variance.

**Q7: How is Random Forest different from Gradient Boosting?**
A: Random Forest trains trees independently in parallel on bootstrap samples (bagging). Gradient Boosting trains trees sequentially, each correcting the errors of the previous. Random Forest reduces variance (great for noisy data). Gradient Boosting reduces bias (great for structured data). XGBoost/LightGBM usually outperform Random Forest on tabular data.

**Q8: Can Random Forest handle missing values?**
A: Random Forest can handle missing values in two ways: (1) Using median/mode imputation internally (ranger, randomForest R packages), (2) Using proximity-based imputation. scikit-learn's implementation does NOT handle missing values - impute before training.

**Q9: What is the difference between Random Forest and ExtraTrees?**
A: ExtraTrees (Extremely Randomized Trees) adds more randomness: instead of finding the best split threshold, it randomly selects split thresholds. This further reduces variance at the cost of slightly increased bias. ExtraTrees trains faster and can outperform Random Forest with enough trees.

**Q10: When would you NOT use Random Forest?**
A: (1) Very high-dimensional sparse data (use linear models or gradient boosting). (2) When interpretability is critical (use a single tree). (3) When prediction speed is crucial (deep learning or linear models are faster). (4) When extrapolation is needed (trees cannot predict beyond training range). (5) Very large datasets (inference can be slow with many trees).

**Q11: What is the bias-variance tradeoff in Random Forest?**
A: Individual trees have low bias but high variance. Bagging reduces variance. If trees are too correlated, variance reduction is limited. Randomizing features reduces correlation between trees, improving the ensemble. The optimal number of features (max_features) balances individual tree strength vs tree diversity.

**Q12: How does Random Forest handle imbalanced data?**
A: Options: (1) class_weight='balanced' adjusts weights inversely proportional to class frequencies. (2) Stratified sampling ensures each bootstrap sample preserves class proportions. (3) Balanced Random Forest undersamples majority class in each bootstrap sample. (4) SMOTE + Random Forest for oversampling.
