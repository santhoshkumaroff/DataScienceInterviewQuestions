# Machine Learning Fundamentals

## What is it?
Machine Learning (ML) is a subset of AI where computers learn patterns from data without being explicitly programmed. The three main types are Supervised Learning (labeled data), Unsupervised Learning (unlabeled data), and Reinforcement Learning (reward-based learning). Key concepts include Train/Test split, Overfitting, Underfitting, and the Bias-Variance tradeoff.

## Why do we use it?
ML powers recommendation systems, fraud detection, self-driving cars, medical diagnosis, and natural language processing. Companies use ML to automate decisions, uncover insights from data, and build intelligent products that improve over time.

## Real-Time Example 1
**E-commerce Product Recommendations (Amazon)**
1. Amazon collects user behavior data (clicks, purchases, searches)
2. A supervised model is trained to predict which products a user might buy
3. Data is split into 80% training and 20% testing
4. The model is deployed and serves personalized recommendations
5. Performance is monitored for overfitting (too specific to past data) vs underfitting (too generic)

## Real-Time Example 2
**Self-Driving Cars (Tesla)**
1. Cameras and sensors collect road data (images, lidar, radar)
2. A Reinforcement Learning agent learns to drive by maximizing reward (safe arrival)
3. The model is tested on a hold-out validation set
4. If the model overfits, it fails on new roads; if underfits, it makes basic errors
5. The Bias-Variance tradeoff is tuned for optimal generalization

## Concept Example 1
```python
# Train/Test Split with Overfitting Example
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score
import numpy as np

X = np.random.rand(100, 5)
y = (X[:, 0] + X[:, 1] > 1).astype(int)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Deep tree (overfits)
model = DecisionTreeClassifier(max_depth=10)
model.fit(X_train, y_train)

print(f"Train accuracy: {accuracy_score(y_train, model.predict(X_train)):.2f}")
print(f"Test accuracy: {accuracy_score(y_test, model.predict(X_test)):.2f}")
# Output: Train accuracy: 1.00, Test accuracy: 0.70
# Explanation: High train accuracy but low test accuracy = overfitting
```

## Concept Example 2
```python
# Bias-Variance Tradeoff Visualization
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import PolynomialFeatures
from sklearn.pipeline import make_pipeline
import numpy as np

np.random.seed(42)
X = np.linspace(0, 1, 20).reshape(-1, 1)
y = np.sin(2 * np.pi * X.ravel()) + np.random.normal(0, 0.2, 20)

# High bias (underfitting) - Linear model
model_high_bias = LinearRegression()
model_high_bias.fit(X, y)

# High variance (overfitting) - 15-degree polynomial
model_high_var = make_pipeline(PolynomialFeatures(15), LinearRegression())
model_high_var.fit(X, y)

print(f"Linear model score: {model_high_bias.score(X, y):.2f} (High Bias)")
print(f"Poly model score: {model_high_var.score(X, y):.2f} (High Variance)")
# Output: Linear model score: 0.52, Poly model score: 0.99
# Explanation: Linear underfits (high bias), polynomial overfits (high variance)
```

## Common Mistakes
- Using 100% of data for training with no test set
- Tuning hyperparameters based on test set performance (data leakage)
- Assuming more features always improves performance
- Ignoring the Bias-Variance tradeoff when selecting model complexity
- Not shuffling data before splitting (especially time-series)

## How Interviewers Ask This
- "Explain the difference between supervised and unsupervised learning."
- "What is overfitting and how do you prevent it?"
- "Describe the Bias-Variance tradeoff with an example."
- "Why do we split data into train, validation, and test sets?"
- "What happens if your training accuracy is 99% but test accuracy is 60%?"

## How To Impress The Interviewer
Discuss the Bias-Variance decomposition mathematically: Expected Error = Bias² + Variance + Irreducible Error. Explain that ensemble methods (Random Forest, Gradient Boosting) reduce variance while keeping bias low. Mention that deep learning models have low bias but very high variance, requiring large datasets and regularization. Talk about production concerns: data drift, concept drift, and the need for continuous monitoring after deployment.

## Most Asked Questions

**Q1: What is the difference between supervised and unsupervised learning?**
A: Supervised learning uses labeled data (input-output pairs) to learn a mapping function. Examples: regression, classification. Unsupervised learning finds patterns in unlabeled data. Examples: clustering, dimensionality reduction. Real-world: Supervised = spam detection (labeled emails), Unsupervised = customer segmentation (no labels).

**Q2: What is overfitting and how do you prevent it?**
A: Overfitting occurs when a model learns noise in the training data and fails to generalize. Prevention: cross-validation, regularization (L1/L2), pruning, more training data, feature selection, early stopping. Example: A decision tree with depth 50 that memorizes every training sample but fails on new data.

**Q3: What is underfitting?**
A: Underfitting occurs when a model is too simple to capture patterns in data. The model performs poorly on both training and test sets. Solution: increase model complexity, add features, reduce regularization. Example: Using linear regression on highly non-linear data.

**Q4: Explain the Bias-Variance tradeoff.**
A: Bias is error from wrong assumptions (underfitting). Variance is error from sensitivity to training data (overfitting). As model complexity increases, bias decreases but variance increases. The optimal model minimizes total error. Example: A linear model has high bias (misses patterns), a deep tree has high variance (learns noise).

**Q5: Why do we need a test set?**
A: The test set simulates real-world unseen data. It provides an unbiased evaluation of model performance. Without it, we cannot know if the model generalizes. Example: A model that memorized all training data gets 100% train accuracy but 50% test accuracy - the test set reveals the failure.

**Q6: What is cross-validation?**
A: Cross-validation splits data into k folds, trains on k-1 folds, and validates on the remaining fold. This repeats k times. It gives a more reliable performance estimate than a single train/test split. Example: 5-fold CV on 1000 samples uses 800 for training and 200 for validation in each round.

**Q7: What is the difference between validation set and test set?**
A: The validation set is used for hyperparameter tuning and model selection. The test set is used only once at the end to estimate real-world performance. Using test data for tuning causes information leakage and overestimates performance.

**Q8: Explain Reinforcement Learning.**
A: RL involves an agent learning by interacting with an environment, receiving rewards or penalties. Key terms: state, action, reward, policy. Example: AlphaGo learned to play Go by playing millions of games and maximizing the win reward.

**Q9: What is regularization and why is it used?**
A: Regularization adds a penalty term to the loss function to prevent overfitting. L1 (Lasso) creates sparse models by shrinking some coefficients to zero. L2 (Ridge) shrinks all coefficients evenly. Example: Ridge regression on 100 features prevents any single feature from dominating.

**Q10: What is the difference between parametric and non-parametric models?**
A: Parametric models have fixed parameters (e.g., linear regression coefficients). Non-parametric models' complexity grows with data (e.g., k-NN, decision trees). Parametric models have high bias but low variance; non-parametric have low bias but high variance.
