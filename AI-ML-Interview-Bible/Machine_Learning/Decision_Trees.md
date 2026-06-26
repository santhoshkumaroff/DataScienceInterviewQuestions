# Decision Trees

## What is it?
A Decision Tree is a flowchart-like structure where internal nodes test features, branches represent outcomes, and leaves make predictions. It splits data recursively to maximize purity. Key concepts: Gini Impurity (probability of misclassification), Entropy (measure of disorder), Information Gain (reduction in entropy after a split), Pruning (removing branches to prevent overfitting), and Overfitting (tree memorizes noise in training data).

## Why do we use it?
Decision Trees are highly interpretable - the entire model can be visualized and explained to non-technical stakeholders. No feature scaling is needed. Used in: credit risk assessment (banks must explain rejections), medical diagnosis, customer segmentation, and as building blocks for Random Forests and Gradient Boosting.

## Real-Time Example 1
**Loan Approval (Wells Fargo)**
1. Root node: "Annual Income > $50K?" Yes → right, No → left
2. Right branch: "Credit Score > 700?" Yes → Approve, No → "Debt Ratio < 30%?"
3. Left branch: "Has Collateral?" Yes → "Loan Amount < $20K?", No → Deny
4. Each leaf has a decision: Approve or Deny
5. The tree explains exactly why a loan was denied (e.g., low income + no collateral)

## Real-Time Example 2
**Customer Churn Prediction (AT&T)**
1. Root: "Contract Type = Monthly?" Yes (high churn) → continue, No (1-year/2-year) → low churn
2. Monthly branch: "Customer Service Calls > 3?" Yes → "High churn risk", No → "Medium churn risk"
3. The tree reveals insights: monthly contract + 4+ service calls = 80% churn probability
4. AT&T targets these customers with retention offers
5. Pruning removes overfitted branches for better generalization

## Concept Example 1
```python
# Decision Tree Classification
from sklearn.tree import DecisionTreeClassifier, plot_tree
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import numpy as np

np.random.seed(42)
X = np.random.randn(200, 2)
y = (X[:, 0]**2 + X[:, 1]**2 > 1.5).astype(int)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# Tree with different depths
for depth in [1, 3, 5, 10, None]:
    tree = DecisionTreeClassifier(max_depth=depth, random_state=42)
    tree.fit(X_train, y_train)
    train_acc = accuracy_score(y_train, tree.predict(X_train))
    test_acc = accuracy_score(y_test, tree.predict(X_test))
    print(f"Depth {str(depth):5s}: Train={train_acc:.3f}, Test={test_acc:.3f}")

# Output:
# Depth 1    : Train=0.769, Test=0.750
# Depth 3    : Train=0.875, Test=0.875
# Depth 5    : Train=0.912, Test=0.900
# Depth 10   : Train=0.994, Test=0.925
# Depth None : Train=1.000, Test=0.925
# Explanation: depth=3 gives good balance, deeper trees overfit
```

## Concept Example 2
```python
# Gini Impurity vs Entropy Comparison
from sklearn.tree import DecisionTreeClassifier
from sklearn.datasets import make_classification
from sklearn.model_selection import cross_val_score
import numpy as np

X, y = make_classification(n_samples=500, n_features=5, random_state=42)

for criterion in ['gini', 'entropy']:
    tree = DecisionTreeClassifier(criterion=criterion, max_depth=5, random_state=42)
    scores = cross_val_score(tree, X, y, cv=5)
    print(f"{criterion:8s}: Mean CV Accuracy = {scores.mean():.3f}")

# Output:
# gini    : Mean CV Accuracy = 0.898
# entropy : Mean CV Accuracy = 0.894
# Explanation: Gini and entropy often give very similar results
# Gini is slightly faster to compute
```

## Common Mistakes
- Not setting max_depth (tree grows until pure, overfitting badly)
- Not pruning the tree (allows memorization of noise)
- Using trees with high cardinality categorical features (creates bias)
- Not handling class imbalance (tree may always predict majority class)
- Assuming trees can extrapolate (they can only predict within training range)
- Ignoring feature importance correlation (correlated features split importance)

## How Interviewers Ask This
- "How does a Decision Tree decide where to split?"
- "What is the difference between Gini Impurity and Entropy?"
- "What is pruning and why is it important?"
- "How do you handle overfitting in Decision Trees?"
- "Explain Information Gain with an example."

## How To Impress The Interviewer
Discuss that Decision Trees are non-parametric and can fit any data perfectly (zero bias, infinite variance without pruning). Explain the computational complexity: building a tree is O(n×d×log n) but finding the optimal tree is NP-complete (greedy splitting used in practice). Discuss handling of missing values: surrogate splits. Mention that trees are unstable (high variance) - small data changes can create completely different trees (hence ensembling helps). Talk about the difference between CART (binary splits) and ID3/C4.5 (multi-way splits for categorical features).

## Most Asked Questions

**Q1: How does a Decision Tree decide where to split?**
A: The tree evaluates all possible split points for all features and chooses the one that maximizes purity (minimizes Gini or maximizes Information Gain). It selects the feature and threshold that best separates the classes. The process repeats recursively until a stopping criterion is met.

**Q2: What is Gini Impurity?**
A: Gini = 1 - Σ pᵢ² where pᵢ is the proportion of class i in a node. It measures the probability of mislabeling a random sample. Pure node (all same class): Gini = 0. Worst case (equal classes): Gini = 0.5. Lower Gini = better split.

**Q3: What is Entropy and Information Gain?**
A: Entropy = -Σ pᵢ log₂(pᵢ) measures disorder. Pure node: entropy = 0. Equal split: entropy = 1. Information Gain = Entropy(parent) - Weighted Avg Entropy(children). Higher Information Gain = better split. The tree chooses the split with maximum Information Gain.

**Q4: What is the difference between Gini Impurity and Entropy?**
A: Both measure node impurity. Gini ranges [0, 0.5], Entropy ranges [0, 1]. Gini is slightly faster to compute. In practice, they give very similar results. Gini tends to isolate the most frequent class, while entropy tends to produce more balanced splits.

**Q5: What is pruning?**
A: Pruning removes branches from a fully grown tree to reduce overfitting. Pre-pruning (early stopping): stop splitting when a node has fewer than min_samples_split samples. Post-pruning (cost complexity pruning): grow full tree, then remove branches that provide little improvement. scikit-learn uses pre-pruning via hyperparameters.

**Q6: How do you prevent overfitting in Decision Trees?**
A: Set max_depth (limit tree depth), min_samples_split (minimum samples to split), min_samples_leaf (minimum samples per leaf), max_features (limit features considered per split), max_leaf_nodes (limit total leaves), and apply pruning. Cross-validate to find optimal hyperparameters.

**Q7: What are the hyperparameters of Decision Trees?**
A: Key hyperparameters: max_depth (tree depth), min_samples_split (minimum samples to split), min_samples_leaf (minimum samples per leaf), max_features (features per split), criterion (gini/entropy), max_leaf_nodes, min_impurity_decrease. These control the bias-variance tradeoff.

**Q8: Why are Decision Trees called "white-box" models?**
A: Because their decisions are completely transparent and interpretable. You can trace any prediction through the tree's path: "Income > $50K → Credit Score > 700 → Approved." This is crucial for regulated industries (banking, healthcare) where models must explain decisions.

**Q9: How do Decision Trees handle categorical features?**
A: scikit-learn's DecisionTreeClassifier does not handle categorical features natively - you must one-hot encode them. However, the tree can naturally split on categorical features (creating branches for categories). For high-cardinality categories, histogram-based splits can be used.

**Q10: What are the advantages and disadvantages of Decision Trees?**
A: Advantages: interpretable, no scaling needed, handles non-linear relationships, captures feature interactions. Disadvantages: high variance (unstable), prone to overfitting, cannot extrapolate, biased toward features with many levels, piecewise constant predictions (not smooth).

**Q11: What is a regression tree?**
A: A regression tree predicts continuous values instead of classes. Leaf nodes contain the average value of training samples in that leaf. Splits minimize MSE instead of Gini. Example: Predicting house price: root splits on "sqft > 2000", leaves contain average price in each region.

**Q12: How does a Decision Tree handle missing values?**
A: scikit-learn's DecisionTree does not handle missing values natively. You must impute them before training. Some implementations (like C4.5) use "surrogate splits": find alternative features that produce similar splits. Production systems often use imputation + tree-based approaches.
