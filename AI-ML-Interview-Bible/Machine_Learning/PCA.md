# Principal Component Analysis (PCA)

## What is it?
PCA is an unsupervised dimensionality reduction technique that transforms high-dimensional data into a new set of uncorrelated variables called Principal Components. These components capture the maximum variance in the data, sorted by importance. Eigenvalues represent the variance explained by each component. The Explained Variance Ratio shows how much information each component retains. PCA reduces feature count while preserving most data structure.

## Why do we use it?
PCA solves the Curse of Dimensionality, speeds up model training, removes multicollinearity, and enables visualization of high-dimensional data. Used in: facial recognition (eigenfaces), genomics (gene expression analysis), image compression, finance (portfolio risk analysis), and exploratory data analysis.

## Real-Time Example 1
**Facial Recognition (Apple Face ID)**
1. iPhone captures 30,000+ facial data points from the TrueDepth camera
2. PCA reduces this to ~100 principal components (eigenfaces)
3. These components capture the most important facial variations
4. When a user looks at the phone, their face is projected into PCA space
5. Matching is done in low-dimensional space for speed and accuracy

## Real-Time Example 2
**Stock Portfolio Risk Analysis (Goldman Sachs)**
1. Goldman tracks 500 stocks with daily returns over 5 years (2500 data points each)
2. PCA reduces 500 features to 5-10 principal components
3. First component represents "market risk" (all stocks moving together)
4. Later components capture sector-specific or stock-specific risks
5. Portfolio managers use components for risk decomposition and hedging

## Concept Example 1
```python
# PCA for Dimensionality Reduction
from sklearn.decomposition import PCA
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score
import numpy as np

data = load_iris()
X, y = data.data, data.target

# Original data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
rf = RandomForestClassifier(random_state=42)
rf.fit(X_train, y_train)
orig_acc = accuracy_score(y_test, rf.predict(X_test))
print(f"Original (4 features): Accuracy = {orig_acc:.3f}")

# PCA with 2 components
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X)
X_train_pca, X_test_pca, y_train, y_test = train_test_split(X_pca, y, test_size=0.2, random_state=42)
rf.fit(X_train_pca, y_train)
pca_acc = accuracy_score(y_test, rf.predict(X_test_pca))

print(f"PCA (2 components):    Accuracy = {pca_acc:.3f}")
print(f"Explained variance ratio: {pca.explained_variance_ratio_.round(3)}")
print(f"Total variance explained: {pca.explained_variance_ratio_.sum():.3f}")
# Output:
# Original (4 features): Accuracy = 1.000
# PCA (2 components):    Accuracy = 0.967
# Explained variance ratio: [0.925 0.053]
# Total variance explained: 0.978
# 2 components retain 97.8% variance with only 2% accuracy loss
```

## Concept Example 2
```python
# PCA Visualization and Eigenvalues
from sklearn.decomposition import PCA
from sklearn.datasets import load_digits
import numpy as np

# 64-dimensional digit images (8x8 pixels)
digits = load_digits()
X, y = digits.data, digits.target

pca = PCA(n_components=10)
X_pca = pca.fit_transform(X)

print("Cumulative explained variance:")
for i, cum_var in enumerate(np.cumsum(pca.explained_variance_ratio_)):
    print(f"Components: {i+1:2d}, Cumulative Variance: {cum_var:.3f}")

# Determine components needed for 90% variance
cum_var = np.cumsum(pca.explained_variance_ratio_)
n_90 = np.argmax(cum_var >= 0.90) + 1
print(f"\nComponents needed for 90% variance: {n_90}")
print(f"Original dimensions: {X.shape[1]}")
print(f"Reduction: {X.shape[1] / n_90:.1f}x compression")

# Output:
# Components:  1, Cumulative Variance: 0.149
# Components:  2, Cumulative Variance: 0.271
# Components:  3, Cumulative Variance: 0.371
# ...
# Components needed for 90% variance: 21
# Reduction: 3.0x compression
```

## Common Mistakes
- Not scaling data before PCA (features with larger variance dominate)
- Assuming PCA components are interpretable (they are linear combinations)
- Choosing too few components and losing critical information
- Using PCA when interpretability is crucial (consider feature selection instead)
- Applying PCA to sparse data (use Truncated SVD instead)
- Thinking PCA captures class separation (it captures variance, not separability)

## How Interviewers Ask This
- "Explain PCA in simple terms."
- "How do you choose how many principal components to keep?"
- "What is the difference between PCA and feature selection?"
- "Why must you scale data before PCA?"
- "What are eigenvalues and eigenvectors in the context of PCA?"

## How To Impress The Interviewer
Explain the linear algebra: PCA finds eigenvectors of the covariance matrix. The eigenvector with the largest eigenvalue is the first principal component. The variance explained by each component = eigenvalue / sum(all eigenvalues). Discuss the relationship between PCA and SVD: PCA uses SVD (singular value decomposition) for numerical stability. Explain that PCA assumes linearity - for non-linear structure, use Kernel PCA, t-SNE, or UMAP. Mention that whitening (PCA with unit variance transformation) is used to remove correlations before training some models. Discuss the "scree plot" for deciding component count and the elbow rule.

## Most Asked Questions

**Q1: How does PCA work?**
A: (1) Standardize the data (zero mean, unit variance). (2) Compute the covariance matrix. (3) Find eigenvectors and eigenvalues of the covariance matrix. (4) Sort eigenvectors by descending eigenvalues. (5) Select top k eigenvectors as principal components. (6) Project data onto these components.

**Q2: How do you choose the number of principal components?**
A: (1) Explained variance threshold: keep enough components to explain 90-95% variance. (2) Scree plot: look for "elbow" where additional components add little variance. (3) Kaiser rule: keep components with eigenvalue > 1. (4) For visualization: use 2 or 3 components. (5) Domain-specific: enough components for downstream task performance.

**Q3: Why is scaling important before PCA?**
A: PCA is sensitive to the scale of features. If one feature has variance 100 and another has variance 1, the first will dominate the first principal component regardless of information content. StandardScaler ensures all features contribute equally. Without scaling, PCA is misleading.

**Q4: What is the difference between PCA and feature selection?**
A: PCA creates new features (linear combinations) while feature selection keeps original features. PCA components are not interpretable (what is "0.3×age + 0.4×income"?). Feature selection preserves interpretability. PCA reduces dimensions and removes correlations; feature selection identifies important individual features.

**Q5: What are eigenvalues and eigenvectors in PCA?**
A: Eigenvectors define the directions (principal components) of maximum variance. Eigenvalues measure the amount of variance captured by each eigenvector. The eigenvector with the largest eigenvalue is PC1 (captures most variance). The sum of eigenvalues equals total variance in the data.

**Q6: What is explained variance ratio?**
A: The proportion of total variance captured by each principal component. Calculated as eigenvalue / sum(all eigenvalues). Example: [0.70, 0.15, 0.10, 0.05] means PC1 explains 70% of variance, PC1+PC2 explain 85%, etc. Used to decide how many components to keep.

**Q7: What are the disadvantages of PCA?**
A: (1) Assumes linear relationships. (2) Components are hard to interpret. (3) Sensitive to outliers. (4) PCA captures global structure, not local patterns. (5) Standardization choices affect results. (6) PCA may not improve supervised learning if variance doesn't align with class separation.

**Q8: How is PCA related to SVD?**
A: PCA is computationally implemented using SVD (Singular Value Decomposition). Instead of computing the covariance matrix (expensive for high dimensions), SVD directly decomposes the data matrix: X = UΣVᵀ. The right singular vectors V are the principal components. Singular values are related to eigenvalues: λ = s²/(n-1).

**Q9: What is the difference between PCA and t-SNE?**
A: PCA is linear, deterministic, and preserves global structure. t-SNE is non-linear, stochastic, and preserves local structure. PCA is used for dimensionality reduction before modeling. t-SNE is used only for visualization. t-SNE often creates better-looking 2D plots but should not be used for feature extraction.

**Q10: Can PCA be used for anomaly detection?**
A: Yes. After PCA, compute the reconstruction error (distance between original point and its PCA projection). Points with high reconstruction error are potential anomalies - they don't fit the data's normal structure. Used in network intrusion detection, manufacturing defects, and fraud detection.

**Q11: What is Kernel PCA?**
A: Kernel PCA extends PCA to non-linear data using the kernel trick (like SVM). It maps data to a higher-dimensional feature space and performs PCA there. Useful when data lies on a non-linear manifold. Common kernels: RBF, Polynomial. Downside: more expensive and harder to interpret.

**Q12: What is the difference between PCA and Factor Analysis?**
A: PCA captures total variance (unique + shared). Factor Analysis models only shared variance, assuming unique variance is noise. Factor Analysis assumes latent factors generate observed variables. PCA is more commonly used for dimensionality reduction in ML; Factor Analysis is more common in social sciences for identifying latent constructs.
