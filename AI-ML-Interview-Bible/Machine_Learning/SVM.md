# Support Vector Machines (SVM)

## What is it?
SVM finds the optimal hyperplane that maximally separates classes in feature space. Support Vectors are the critical data points closest to the hyperplane. The Kernel Trick (RBF, Polynomial, Linear) projects data into higher dimensions to make non-linear problems separable. The margin is the distance between the hyperplane and the nearest support vectors. The C parameter controls the tradeoff between maximizing margin and minimizing misclassifications (Soft Margin).

## Why do we use it?
SVM is powerful for high-dimensional data and problems where clear margin separation exists. It works well with limited data. Used in: image classification, text categorization, face detection, bioinformatics (protein classification), and handwriting recognition. SVMs were the state-of-the-art before deep learning.

## Real-Time Example 1
**Face Detection (Facebook)**
1. Facebook extracts pixel features from face images
2. SVM with RBF kernel finds a non-linear boundary between faces and non-faces
3. Support vectors are the most ambiguous face/non-face images
4. The C parameter is tuned: low C = more false positives, high C = more false negatives
5. The trained SVM classifies which photos contain faces for tagging

## Real-Time Example 2
**Cancer Detection (Memorial Sloan Kettering)**
1. Gene expression data has thousands of features but only 100 patients
2. SVM handles high-dimensional, low-sample data well
3. Linear kernel separates cancer vs healthy tissue samples
4. Support vectors identify the most informative genes for diagnosis
5. The model achieves 95% accuracy on biopsy classification

## Concept Example 1
```python
# SVM with Different Kernels
from sklearn.svm import SVC
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.preprocessing import StandardScaler
import numpy as np

np.random.seed(42)
X = np.random.randn(300, 2)
y = (X[:, 0]**2 + X[:, 1]**2 > 1.5).astype(int)  # non-linear boundary

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# Scale features (crucial for SVM)
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

for kernel in ['linear', 'poly', 'rbf', 'sigmoid']:
    svm = SVC(kernel=kernel, C=1.0, random_state=42)
    svm.fit(X_train_scaled, y_train)
    acc = accuracy_score(y_test, svm.predict(X_test_scaled))
    print(f"Kernel={kernel:8s}: Accuracy = {acc:.3f}")

# Output:
# Kernel=linear   : Accuracy = 0.583
# Kernel=poly     : Accuracy = 0.867
# Kernel=rbf      : Accuracy = 0.950
# Kernel=sigmoid  : Accuracy = 0.500
# Explanation: RBF kernel captures non-linear boundary best
```

## Concept Example 2
```python
# Effect of C Parameter on Decision Boundary
from sklearn.svm import SVC
from sklearn.datasets import make_classification
from sklearn.preprocessing import StandardScaler
import numpy as np

np.random.seed(42)
X, y = make_classification(n_samples=200, n_features=2, n_redundant=0,
                           n_clusters_per_class=1, class_sep=0.8, random_state=42)

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

for C in [0.01, 0.1, 1, 10, 100]:
    svm = SVC(kernel='rbf', C=C, random_state=42)
    svm.fit(X_scaled, y)
    train_acc = svm.score(X_scaled, y)
    print(f"C={C:5}: Train Accuracy = {train_acc:.3f}, Support Vectors = {len(svm.support_)}")

# Output:
# C= 0.01: Train Accuracy = 0.710, Support Vectors = 95
# C=  0.1: Train Accuracy = 0.785, Support Vectors = 90
# C=    1: Train Accuracy = 0.880, Support Vectors = 68
# C=   10: Train Accuracy = 0.935, Support Vectors = 52
# C=  100: Train Accuracy = 0.950, Support Vectors = 39
# Explanation: Higher C = fewer support vectors, tighter boundary, higher training accuracy
```

## Common Mistakes
- Not scaling features (SVM is very sensitive to feature scales)
- Using linear kernel for non-linear problems
- Choosing C incorrectly (underfitting or overfitting)
- Not understanding the Kernel Trick conceptually
- Using SVM on very large datasets (O(n²) to O(n³) complexity)
- Not tuning gamma for RBF kernel (controls influence radius)

## How Interviewers Ask This
- "Explain the concept of margin in SVM."
- "What are support vectors and why do they matter?"
- "Explain the Kernel Trick in simple terms."
- "What is the role of the C parameter?"
- "What is the difference between hard margin and soft margin SVM?"

## How To Impress The Interviewer
Discuss the mathematical formulation: SVM finds hyperplane that maximizes 2/||w|| (margin width) subject to yᵢ(w·xᵢ + b) ≥ 1. Explain the dual formulation and why it enables the kernel trick (the optimization only depends on dot products). Discuss that RBF kernel is the default choice because it's a universal approximator. Explain the gamma parameter: small gamma = broad influence (smooth boundary), large gamma = narrow influence (complex boundary, risks overfitting). Mention that SVM can produce non-probabilistic outputs - Platt scaling is needed for probabilities. Discuss the Nu-SVM variant where nu controls the upper bound on training errors.

## Most Asked Questions

**Q1: How does SVM work?**
A: SVM finds the hyperplane that maximizes the margin between classes. The margin is the distance between the hyperplane and the closest data points (support vectors). This maximum-margin hyperplane provides the best separation and generalization.

**Q2: What are support vectors?**
A: Support vectors are the training examples that lie closest to the decision boundary (hyperplane). They are the most critical points - if you remove them, the hyperplane changes. Non-support vectors don't affect the boundary. This makes SVM memory-efficient (only stores support vectors for prediction).

**Q3: What is the Kernel Trick?**
A: The Kernel Trick maps data to a higher-dimensional space without explicitly computing the transformation. It computes dot products in the transformed space using the kernel function. This allows SVM to find non-linear decision boundaries while keeping computational cost low. Popular kernels: RBF (Gaussian), Polynomial, Sigmoid.

**Q4: What is the difference between hard margin and soft margin SVM?**
A: Hard margin SVM assumes data is perfectly separable (no misclassifications). Soft margin SVM allows some misclassifications by introducing a slack variable. The C parameter controls the penalty for misclassifications. In practice, use soft margin (C > 0) because real data is rarely perfectly separable.

**Q5: Explain the C parameter.**
A: C controls the tradeoff between maximizing margin and minimizing misclassifications. Large C: penalizes misclassifications heavily (narrow margin, may overfit). Small C: allows more misclassifications for wider margin (simpler model, may underfit). Tune C via cross-validation. Typical range: 0.001 to 1000.

**Q6: What is the RBF kernel and what is gamma?**
A: RBF (Radial Basis Function) = exp(-γ||x - x'||²). It measures similarity between two points based on distance. Gamma controls influence radius: low gamma → each point influences far away (smooth boundary), high gamma → each point only influences nearby (complex boundary, may overfit).

**Q7: Why is feature scaling important for SVM?**
A: SVM seeks to maximize margin, which depends on distance calculations. Features with larger scales dominate the distance computation. StandardScaler (zero mean, unit variance) ensures all features contribute equally. Without scaling, SVM results can be completely misleading.

**Q8: What are the advantages and disadvantages of SVM?**
A: Advantages: effective in high dimensions, memory efficient (uses support vectors), versatile kernels, good generalization with limited data. Disadvantages: doesn't scale well to large datasets (O(n²) to O(n³)), no direct probability estimates, sensitive to feature scaling, choosing the right kernel requires expertise.

**Q9: How does SVM handle multi-class classification?**
A: SVM is inherently binary. For multi-class: One-vs-One (trains K(K-1)/2 classifiers, each pair of classes) or One-vs-Rest (trains K classifiers, each class vs all others). sklearn uses One-vs-One by default. For many classes, One-vs-Rest is more efficient.

**Q10: What is the difference between SVM and Logistic Regression?**
A: SVM focuses on points near the decision boundary (support vectors), while Logistic Regression considers all points. SVM minimizes hinge loss, Logistic Regression minimizes log loss. SVM aims to maximize margin; Logistic Regression maximizes likelihood. With well-separated classes, SVM often outperforms. With overlapping classes, Logistic Regression can be better.

**Q11: What is hinge loss?**
A: Hinge loss = max(0, 1 - yᵢ(w·xᵢ + b)). It penalizes misclassified points AND correctly classified points that are too close to the decision boundary. This encourages a larger margin. SVM with hinge loss is equivalent to minimizing ||w||² + C × Σ hinge loss.

**Q12: Can SVM be used for regression?**
A: Yes, Support Vector Regression (SVR) extends SVM to regression. Instead of maximizing margin, SVR finds a tube of width ε around the predictions. Points inside the tube have zero loss. SVR is used for time series forecasting, financial prediction, and other regression tasks where SVMs excel.
