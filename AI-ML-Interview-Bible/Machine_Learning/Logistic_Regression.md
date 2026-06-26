# Logistic Regression

## What is it?
Logistic Regression is a classification algorithm that predicts the probability of a binary outcome using the Sigmoid function: P(y=1) = 1 / (1 + e^-(β₀ + β₁X)). The decision boundary (typically 0.5) separates classes. It uses Log Loss (cross-entropy) as the cost function. The Confusion Matrix evaluates performance using True Positives, True Negatives, False Positives, and False Negatives.

## Why do we use it?
Logistic Regression is the go-to binary classification baseline. It is fast, interpretable, and provides calibrated probabilities. Used in: spam detection, fraud detection, customer churn prediction, medical diagnosis. Banks use it to approve/deny loans. Its coefficients show feature impact on the log-odds scale.

## Real-Time Example 1
**Loan Default Prediction (JPMorgan Chase)**
1. Chase collects applicant data: income, credit score, debt ratio, employment years
2. Logistic Regression predicts P(Default = Yes) using these features
3. A threshold of 0.5: P ≥ 0.5 = deny loan, P < 0.5 = approve
4. The Confusion Matrix shows: 95% of defaults caught, 3% false alarms
5. Feature coefficients reveal: credit score has the largest impact on default probability

## Real-Time Example 2
**Email Spam Detection (Yahoo Mail)**
1. Yahoo extracts features: words in email, sender reputation, time of day
2. P(Spam) = σ(β₀ + β₁(word_count) + β₂(suspicious_words) + β₃(sender_score))
3. Emails with P(Spam) > 0.9 go directly to spam folder
4. The model is retrained weekly on user feedback (mark as spam / not spam)
5. Log Loss is monitored to detect model drift

## Concept Example 1
```python
# Logistic Regression with Sigmoid
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import confusion_matrix, log_loss, accuracy_score
import numpy as np

X = np.array([[1], [2], [3], [4], [5], [6], [7], [8], [9], [10]])
y = np.array([0, 0, 0, 0, 1, 0, 1, 1, 1, 1])  # binary labels

model = LogisticRegression()
model.fit(X, y)

# Get probabilities
probs = model.predict_proba(X)[:, 1]
preds = model.predict(X)

print(f"Coefficient: {model.coef_[0][0]:.2f}")
print(f"Intercept: {model.intercept_[0]:.2f}")
print(f"Probabilities: {probs.round(3)}")
print(f"Predictions: {preds}")
print(f"Accuracy: {accuracy_score(y, preds):.2f}")
print(f"Log Loss: {log_loss(y, probs):.3f}")
# Output: Coefficient: 0.89, Intercept: -3.95
# Probabilities: [0.05, 0.11, 0.22, 0.39, 0.59, 0.76, 0.88, 0.94, 0.97, 0.99]
# Predictions: [0 0 0 0 1 1 1 1 1 1]
# Explanation: Decision boundary at ~4.5, larger X gives higher probability
```

## Concept Example 2
```python
# Confusion Matrix and Evaluation
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import confusion_matrix, classification_report
import numpy as np

np.random.seed(42)
X = np.random.randn(200, 2)
y = (X[:, 0] + X[:, 1] + np.random.randn(200) * 0.5 > 0).astype(int)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

model = LogisticRegression()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

cm = confusion_matrix(y_test, y_pred)
tn, fp, fn, tp = cm.ravel()

print("Confusion Matrix:")
print(cm)
print(f"\nAccuracy: {(tp+tn)/(tp+tn+fp+fn):.2f}")
print(f"Precision: {tp/(tp+fp):.2f}")
print(f"Recall: {tp/(tp+fn):.2f}")
print(f"F1 Score: {2*tp/(2*tp+fp+fn):.2f}")
# Output:
# Confusion Matrix:
# [[20  3]
#  [ 2 15]]
# Accuracy: 0.88, Precision: 0.83, Recall: 0.88, F1: 0.86
```

## Common Mistakes
- Using Linear Regression for binary classification
- Not removing multicollinearity among features
- Ignoring class imbalance (accuracy paradox)
- Using accuracy alone when classes are imbalanced
- Changing decision threshold without analyzing precision-recall tradeoff
- Not checking for outliers that affect decision boundary

## How Interviewers Ask This
- "Why can't we use Linear Regression for classification?"
- "Explain the Sigmoid function and why it's used."
- "What is the difference between Log Loss and MSE?"
- "How do you handle imbalanced classes in Logistic Regression?"
- "What is the decision boundary and how do you choose the threshold?"

## How To Impress The Interviewer
Explain that Logistic Regression is actually a linear model in the log-odds space: log(p/(1-p)) = β₀ + β₁X. Discuss regularization (L1, L2, Elastic Net) for Logistic Regression. Explain the relationship between Log Loss and Maximum Likelihood Estimation. Talk about multinomial logistic regression (softmax) for multi-class problems. Discuss calibration (Platt scaling, isotonic regression) and why well-calibrated probabilities matter in production. Mention that for large-scale problems (millions of features), SGDClassifier with log loss is used.

## Most Asked Questions

**Q1: Why can't we use Linear Regression for binary classification?**
A: Linear Regression predictions are unbounded (can be < 0 or > 1), which doesn't make sense for probabilities. Linear Regression assumes errors are normally distributed - violated with binary outcomes. Logistic Regression squashes output between 0 and 1 using the sigmoid function.

**Q2: Explain the Sigmoid function.**
A: Sigmoid = 1 / (1 + e^-z). It maps any real number to a value between 0 and 1. At z=0, σ=0.5. Large positive z gives σ→1, large negative z gives σ→0. It's S-shaped and differentiable, making it suitable for gradient-based optimization.

**Q3: What is the decision boundary in Logistic Regression?**
A: The decision boundary is the threshold (default 0.5) that separates classes. For a threshold of 0.5: P ≥ 0.5 predicts class 1, P < 0.5 predicts class 0. The boundary can be adjusted based on business needs (e.g., lower threshold to catch more fraud cases, accepting more false positives).

**Q4: What is Log Loss (Cross-Entropy)?**
A: Log Loss = -(1/n) Σ [y_i log(p_i) + (1-y_i) log(1-p_i)]. It penalizes confident wrong predictions heavily. A perfect model has Log Loss = 0. Random guessing gives Log Loss ≈ 0.693 (for balanced classes). It's the standard loss function for classification problems.

**Q5: What is the Confusion Matrix?**
A: A 2×2 table showing: TP (correctly predicted positive), TN (correctly predicted negative), FP (false alarm - predicted positive but actually negative), FN (miss - predicted negative but actually positive). All other metrics (accuracy, precision, recall, F1) are derived from these four values.

**Q6: How do you handle imbalanced classes?**
A: Techniques: (1) Resampling - SMOTE (oversampling minority), undersampling majority. (2) Class weights - assign higher weight to minority class errors. (3) Adjust decision threshold - lower threshold if catching positives is critical. (4) Use precision-recall curves instead of ROC. (5) Collect more minority class data.

**Q7: What is the difference between precision and recall?**
A: Precision = TP/(TP+FP) - of all positive predictions, how many were correct. Recall = TP/(TP+FN) - of all actual positives, how many were caught. Precision focuses on avoiding false alarms; recall focuses on catching all positives. Tradeoff: increasing one typically decreases the other.

**Q8: What is the F1 score?**
A: F1 = 2 × (Precision × Recall) / (Precision + Recall). It's the harmonic mean of precision and recall, balancing both. Useful when classes are imbalanced. F1 = 1 is perfect, F1 = 0 is worst.

**Q9: What is the ROC curve?**
A: ROC (Receiver Operating Characteristic) plots True Positive Rate (Recall) vs False Positive Rate at various thresholds. AUC (Area Under Curve) summarizes model performance. AUC = 1 is perfect, AUC = 0.5 is random guessing. ROC is less sensitive to class imbalance than accuracy.

**Q10: How do you interpret Logistic Regression coefficients?**
A: Coefficients represent the change in log-odds per unit change in the feature. Exponentiating gives the odds ratio. β = 0.5 means: a one-unit increase in X multiplies the odds of y=1 by e^0.5 = 1.65. Positive coefficients increase probability; negative coefficients decrease it.

**Q11: What is multinomial Logistic Regression?**
A: An extension for multi-class classification (K > 2 classes). Uses the Softmax function: P(y=k) = e^(z_k) / Σ e^(z_j). Instead of one decision boundary, it creates K-1 boundaries. Used for digit recognition (0-9), species classification, etc.

**Q12: What is the difference between one-vs-rest and softmax in multi-class classification?**
A: One-vs-rest trains K binary classifiers (each class vs all others). Softmax handles all classes simultaneously with a single model. Softmax is preferred when classes are mutually exclusive. One-vs-rest works with any binary classifier. LogisticRegression in sklearn uses softmax by default for multi-class.
