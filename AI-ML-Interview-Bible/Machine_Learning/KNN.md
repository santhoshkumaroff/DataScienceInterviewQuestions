# K-Nearest Neighbors (KNN)

## What is it?
KNN is a simple, non-parametric algorithm that classifies or predicts based on the k closest training examples in feature space. It uses distance metrics like Euclidean, Manhattan, or Minkowski distance to find neighbors. The choice of k (number of neighbors) is critical. The algorithm suffers from the Curse of Dimensionality - performance degrades as the number of features increases.

## Why do we use it?
KNN is perfect for small datasets and low-dimensional problems where interpretability matters. It requires no training phase (lazy learner). Used in: recommendation systems (people like you also liked), handwriting recognition, image classification, and anomaly detection. Easy to implement and understand.

## Real-Time Example 1
**Movie Recommendation System (Netflix)**
1. Netflix represents movies as feature vectors (genre scores, runtime, year, rating)
2. When a user rates a movie, KNN finds the k most similar movies (k=10)
3. Similarity is measured using Euclidean distance between feature vectors
4. The top-k similar movies are recommended to the user
5. Users also rate recommendations, providing feedback

## Real-Time Example 2
**Medical Diagnosis (Cleveland Clinic)**
1. Patient data: age, blood pressure, cholesterol, BMI, symptoms
2. KNN compares a new patient to historical patient records
3. With k=5, the algorithm finds the 5 most similar past patients
4. If 4 out of 5 had heart disease, the new patient is flagged as high-risk
5. The doctor reviews the flagged case for further testing

## Concept Example 1
```python
# KNN Classification with Different k Values
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import numpy as np

np.random.seed(42)
X = np.random.randn(150, 2)
y = (X[:, 0]**2 + X[:, 1]**2 > 1.5).astype(int)  # circular decision boundary

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

for k in [1, 3, 5, 10, 20]:
    knn = KNeighborsClassifier(n_neighbors=k)
    knn.fit(X_train, y_train)
    y_pred = knn.predict(X_test)
    acc = accuracy_score(y_test, y_pred)
    print(f"k={k:2d}: Accuracy = {acc:.3f}")

# Output:
# k= 1: Accuracy = 0.900
# k= 3: Accuracy = 0.933
# k= 5: Accuracy = 0.967
# k=10: Accuracy = 0.967
# k=20: Accuracy = 0.900
# Explanation: k=5 or 10 gives best balance, k=1 overfits, k=20 underfits
```

## Concept Example 2
```python
# Distance Metrics Comparison
from sklearn.neighbors import KNeighborsClassifier
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import cross_val_score
import numpy as np

np.random.seed(42)
X = np.random.randn(100, 3)
y = (X[:, 0] + X[:, 1] * 2 > 0).astype(int)

# Scale features (important for KNN!)
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

metrics = ['euclidean', 'manhattan', 'minkowski', 'chebyshev']
for metric in metrics:
    knn = KNeighborsClassifier(n_neighbors=5, metric=metric)
    scores = cross_val_score(knn, X_scaled, y, cv=5)
    print(f"{metric:12s}: Mean CV Accuracy = {scores.mean():.3f} (+/- {scores.std():.3f})")

# Output:
# euclidean   : Mean CV Accuracy = 0.870 (+/- 0.041)
# manhattan   : Mean CV Accuracy = 0.890 (+/- 0.055)
# minkowski   : Mean CV Accuracy = 0.870 (+/- 0.041)
# chebyshev   : Mean CV Accuracy = 0.840 (+/- 0.057)
# Explanation: Manhattan distance performs best here
```

## Common Mistakes
- Not scaling/normalizing features (features with larger ranges dominate)
- Choosing k without cross-validation
- Using KNN on high-dimensional data (curse of dimensionality)
- Using odd k to avoid ties but forgetting even k can work with weighted voting
- Not handling categorical features properly (need encoding + appropriate distance)
- Using KNN on large datasets (slow prediction time)

## How Interviewers Ask This
- "How does KNN work? Walk me through the algorithm."
- "How do you choose the optimal value of k?"
- "What is the Curse of Dimensionality?"
- "Why is feature scaling important for KNN?"
- "What is the difference between KNN for classification vs regression?"

## How To Impress The Interviewer
Discuss the time complexity: KNN has O(1) training but O(n×d) for prediction (n=samples, d=features). Explain KD-Trees and Ball Trees for efficient neighbor search in lower dimensions. Mention that KNN is a universal approximator (can represent any decision boundary given enough data). Discuss weighted KNN (closer neighbors have more vote influence). Explain how the choice of distance metric impacts results based on data structure. Mention that for high-dimensional data, use cosine similarity instead of Euclidean. Discuss approximate nearest neighbor methods (LSH, Annoy, FAISS) for production at scale.

## Most Asked Questions

**Q1: How does the KNN algorithm work?**
A: Steps: (1) Store all training examples, (2) For a new query point, compute distance to all training points, (3) Find the k closest points, (4) For classification: majority vote among k neighbors. For regression: average the values of k neighbors. No training phase - just memorizes data.

**Q2: How do you choose the optimal value of k?**
A: Use cross-validation to test different k values (typically 1 to sqrt(n)). Small k (1-3): low bias, high variance (overfitting). Large k (20+): high bias, low variance (underfitting). Choose k that maximizes validation accuracy. Odd k helps avoid ties but is less important when using distance-weighted voting.

**Q3: What is the Curse of Dimensionality?**
A: As the number of features increases, data becomes sparse in high-dimensional space. Distances between all points become nearly equal - every point seems equally far from every other point. This makes KNN (and other distance-based algorithms) ineffective. Solution: dimensionality reduction (PCA, feature selection).

**Q4: Why is feature scaling crucial for KNN?**
A: KNN relies on distance calculations. If features have different scales (e.g., age 0-100 vs salary 30K-200K), the larger-scale features dominate distance calculations. StandardScaler or MinMaxScaler ensures all features contribute equally. Always scale before KNN.

**Q5: What is the difference between KNN for classification vs regression?**
A: Classification: majority vote of neighbors' class labels. Regression: average (or weighted average) of neighbors' continuous values. In sklearn: KNeighborsClassifier vs KNeighborsRegressor. For regression, k controls smoothness - small k follows noise, large k oversmooths.

**Q6: What is a lazy learner?**
A: KNN is a lazy learner because it does no training - it just stores the training data. All computation happens at prediction time. This is opposite to eager learners (like neural networks) that train a model and then predict quickly. Advantage: easily updated with new data. Disadvantage: slow predictions.

**Q7: What distance metrics are commonly used with KNN?**
A: Euclidean (straight-line distance - L2). Manhattan (city block distance - L1). Minkowski (generalized - parameter p). Chebyshev (maximum coordinate difference). Cosine (angle between vectors, good for text). Hamming (for categorical/binary data). Choice depends on data type and problem.

**Q8: What are the computational challenges of KNN in production?**
A: KNN must compute distance to every training point for each prediction - O(n×d) time. With millions of examples, this is slow. Solutions: (1) KD-Trees for low dimensions, (2) Ball Trees, (3) Approximate Nearest Neighbors (Spotify's Annoy, Facebook's FAISS), (4) Reduce dataset size.

**Q9: How do you handle ties in KNN?**
A: If k is even and classes are equally represented among neighbors: (1) Use odd k, (2) Use distance-weighted voting (closer neighbors get more weight), (3) Randomly choose, (4) Reduce k by 1 to break tie. Distance-weighted voting is the most principled approach.

**Q10: Can KNN be used for anomaly detection?**
A: Yes. Calculate the average distance to k nearest neighbors. Points with unusually large average neighbor distance are potential anomalies. This is intuitive: anomalies are far from normal data. Applied in fraud detection, network intrusion detection, manufacturing quality control.

**Q11: What is the impact of noise on KNN?**
A: Noise (mislabeled samples) can significantly degrade KNN performance, especially with small k. A single noisy point near the decision boundary can flip predictions for many nearby points. Solutions: increase k to smooth out noise, use distance-weighted voting, or clean training data.

**Q12: How does KNN compare to decision trees?**
A: KNN is a non-parametric lazy learner; decision trees are eager learners. KNN captures complex boundaries without training; decision trees create interpretable rules. KNN is sensitive to feature scaling and dimensionality; decision trees are not. KNN prediction is slow; decision trees are fast. KNN works best with small to medium datasets with few features.
