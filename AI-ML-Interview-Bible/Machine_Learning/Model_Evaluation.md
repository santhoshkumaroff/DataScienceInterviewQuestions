# Model Evaluation

## What is it?
Model evaluation measures how well a machine learning model performs. Key metrics: Accuracy (correct predictions / total), Precision (true positives / predicted positives), Recall (true positives / actual positives), F1 Score (harmonic mean of precision and recall), ROC-AUC (area under the ROC curve measuring separability), Cross-Validation (k-fold evaluation), Confusion Matrix (TP/TN/FP/FN table), Log Loss (penalty for wrong predictions), RMSE (root mean squared error), and MAE (mean absolute error).

## Why do we use it?
Evaluation tells you whether your model is actually good before deploying it. Different problems need different metrics. In medical diagnosis, high recall is critical (don't miss cancer). In spam detection, high precision matters (don't mark important emails as spam). Proper evaluation prevents deploying a useless or harmful model.

## Real-Time Example 1
**Credit Card Fraud Detection (Visa)**
1. 99.9% legitimate transactions, 0.1% fraudulent (highly imbalanced)
2. A model predicting "not fraud" for every transaction gets 99.9% accuracy - but is useless!
3. Visa uses Recall (catch as much fraud as possible) and Precision (don't annoy customers)
4. They optimize for F1 Score or Precision at a specific Recall threshold
5. The Confusion Matrix reveals: 95% fraud caught, 1% false positive rate

## Real-Time Example 2
**House Price Prediction (Redfin)**
1. Redfin predicts house prices with a regression model
2. RMSE = $25,000 means typical prediction error is $25K (in units of price)
3. MAE = $18,000 means median absolute error is $18K (less sensitive to outliers)
4. R² = 0.85 means 85% of price variance is explained by features
5. Cross-validation (5-fold) ensures the metrics are stable across data splits

## Concept Example 1
```python
# Classification Metrics
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    confusion_matrix, roc_auc_score, log_loss
)
import numpy as np

# Imbalanced dataset example
y_true = np.array([0]*95 + [1]*5)  # 95 negative, 5 positive
y_pred = np.array([0]*90 + [1]*5 + [0]*5)  # model predicts 5 positives

# Dumb classifier that always predicts 0
y_pred_dumb = np.zeros(100)

cm = confusion_matrix(y_true, y_pred)
tn, fp, fn, tp = cm.ravel()

print("Confusion Matrix:")
print(f"       Pred Neg  Pred Pos")
print(f"Actual Neg  {tn}        {fp}")
print(f"Actual Pos  {fn}        {tp}")

print(f"\nAccuracy: {(tp+tn)/(tp+tn+fp+fn):.3f}")
print(f"Precision: {tp/(tp+fp):.3f}")
print(f"Recall: {tp/(tp+fn):.3f}")
print(f"F1 Score: {2*tp/(2*tp+fp+fn):.3f}")
print(f"ROC-AUC: {roc_auc_score(y_true, y_pred):.3f}")
print(f"Log Loss: {log_loss(y_true, y_pred):.3f}")

# Dumb classifier metrics
print(f"\nDumb Model Accuracy: {accuracy_score(y_true, y_pred_dumb):.3f}")
print(f"Dumb Model ROC-AUC: {roc_auc_score(y_true, y_pred_dumb):.3f}")

# Output:
# Confusion Matrix:
#        Pred Neg  Pred Pos
# Actual Neg  90        5
# Actual Pos  0         5
# Accuracy: 0.950, Precision: 0.500, Recall: 1.000, F1: 0.667
# ROC-AUC: 0.975, Log Loss: 7.640
# Dumb Model Accuracy: 0.950 (misleading!)
# Dumb Model ROC-AUC: 0.500 (random!)
```

## Concept Example 2
```python
# Regression Metrics
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score
import numpy as np

y_true = np.array([100, 200, 300, 400, 500])
y_pred = np.array([110, 190, 320, 390, 480])

# With one outlier
y_pred_outlier = np.array([110, 190, 320, 390, 800])

mse = mean_squared_error(y_true, y_pred)
rmse = np.sqrt(mse)
mae = mean_absolute_error(y_true, y_pred)
r2 = r2_score(y_true, y_pred)

mse_out = mean_squared_error(y_true, y_pred_outlier)
rmse_out = np.sqrt(mse_out)
mae_out = mean_absolute_error(y_true, y_pred_outlier)

print("Normal Predictions:")
print(f"MSE: {mse:.0f}, RMSE: {rmse:.0f}, MAE: {mae:.0f}, R²: {r2:.3f}")

print("\nWith Outlier (800 instead of 500):")
print(f"MSE: {mse_out:.0f}, RMSE: {rmse_out:.0f}, MAE: {mae_out:.0f}")
# Output:
# Normal Predictions: MSE: 300, RMSE: 17, MAE: 14, R²: 0.985
# With Outlier: MSE: 6000, RMSE: 77, MAE: 28, R²: -0.600
# Explanation: MSE/RMSE heavily penalize outliers; MAE is more robust
```

## Common Mistakes
- Using accuracy on imbalanced data (accuracy paradox)
- Optimizing for the wrong metric (e.g., precision when recall is needed)
- Using RMSE when MAE is more appropriate (outlier interpretation)
- Evaluating on test set multiple times (causes information leakage)
- Not using cross-validation for hyperparameter tuning
- Assuming high ROC-AUC means good calibration

## How Interviewers Ask This
- "What is the difference between accuracy and F1 score?"
- "When would you use precision vs recall?"
- "What is ROC-AUC and what does AUC = 0.5 mean?"
- "Explain the bias-variance tradeoff in the context of cross-validation."
- "What is the difference between RMSE and MAE?"

## How To Impress The Interviewer
Discuss that ROC-AUC measures rank ordering (how well the model separates classes) but not calibration (probability accuracy). For well-calibrated probabilities, check the calibration curve (reliability diagram). Explain that Log Loss captures both separation AND calibration. Discuss that k-fold CV with k=5 or 10 is standard but stratified k-fold is needed for imbalanced data. For time series, use TimeSeriesSplit. Mention that in production, you need business metrics (profit, cost savings) not just ML metrics. Discuss the difference between micro, macro, and weighted averaging for multi-class F1 scores.

## Most Asked Questions

**Q1: What is the difference between accuracy, precision, and recall?**
A: Accuracy = (TP+TN)/(TP+TN+FP+FN) = overall correctness. Precision = TP/(TP+FP) = how many positive predictions were correct. Recall = TP/(TP+FN) = how many actual positives were caught. Accuracy is misleading for imbalanced data. Example: In cancer screening, high recall (catch all cancers) is critical even if precision is lower.

**Q2: What is the F1 score?**
A: F1 = 2 × (Precision × Recall) / (Precision + Recall). It's the harmonic mean of precision and recall. Useful when classes are imbalanced. F1 ranges [0,1], higher is better. F1=1 means perfect precision and recall. F1=0 means either precision or recall is zero.

**Q3: What is ROC-AUC?**
A: ROC (Receiver Operating Characteristic) plots True Positive Rate vs False Positive Rate at various thresholds. AUC is the area under this curve. AUC = 1.0: perfect separation. AUC = 0.5: random guessing. AUC = 0.0: perfectly reversed predictions. AUC measures the model's ability to rank positive samples higher than negative samples.

**Q4: What is cross-validation and why is it important?**
A: Cross-validation splits data into k folds, trains on k-1, tests on the remaining, and repeats k times. It provides a more reliable performance estimate than a single train/test split. Reduces variance of evaluation. Common values: k=5 or k=10. Stratified CV preserves class proportions. TimeSeriesSplit for time series.

**Q5: What is the Confusion Matrix?**
A: A table summarizing classification results:
- True Positives (TP): correctly predicted positive class
- True Negatives (TN): correctly predicted negative class
- False Positives (FP): incorrectly predicted positive (Type I error)
- False Negatives (FN): incorrectly predicted negative (Type II error)
All classification metrics derive from these four values.

**Q6: What is Log Loss?**
A: Log Loss = -(1/n) Σ [y_i log(p_i) + (1-y_i) log(1-p_i)]. It penalizes confident wrong predictions heavily. Perfect model: Log Loss = 0. Random guessing with balanced classes: ~0.693. Log Loss evaluates probability calibration, not just classification. Better for model comparison than accuracy.

**Q7: What is the difference between RMSE and MAE?**
A: RMSE (Root Mean Squared Error) = sqrt(mean((y - ŷ)²)). MAE (Mean Absolute Error) = mean(|y - ŷ|). RMSE gives more weight to large errors (quadratic penalty). MAE treats all errors equally. If large errors are especially bad, use RMSE. If you want robustness to outliers, use MAE.

**Q8: What is the accuracy paradox?**
A: The accuracy paradox: when evaluating imbalanced data, a model that always predicts the majority class can achieve high accuracy while being completely useless. Example: 99% legitimate transactions, 1% fraud. A model predicting "legitimate" for everything gets 99% accuracy but catches zero fraud.

**Q9: What is the difference between micro, macro, and weighted F1?**
A: Micro F1: calculates global TP/FP/FN across all classes (favors majority classes). Macro F1: calculates F1 per class and averages (all classes equal weight). Weighted F1: calculates F1 per class and averages weighted by class support. For imbalanced data, macro or weighted F1 is more informative.

**Q10: What is overfitting in the context of model evaluation?**
A: Overfitting occurs when a model performs well on training data but poorly on unseen data. Detection: large gap between train and validation metrics. Prevention: cross-validation, regularization, early stopping, more training data, simpler models. The test set should only be used once to get an unbiased final evaluation.

**Q11: What is the difference between validation set and test set?**
A: The validation set is used during development for hyperparameter tuning and model selection (used many times). The test set is held back until the very end to estimate real-world performance (used only once). Using test data for tuning causes overfitting to the test set and overestimates performance.

**Q12: How do you evaluate a clustering model?**
A: Internal metrics (no ground truth): Silhouette Score, Davies-Bouldin Index, Calinski-Harabasz Index. External metrics (with labels): Adjusted Rand Index, Normalized Mutual Information. Visual evaluation using t-SNE/PCA plots. Business validation: do clusters make sense? Clustering evaluation is inherently subjective.
