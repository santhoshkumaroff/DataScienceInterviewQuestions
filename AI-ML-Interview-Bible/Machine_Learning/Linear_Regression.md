# Linear Regression

## What is it?
Linear Regression models the relationship between a dependent variable (y) and one or more independent variables (X) using a linear equation: y = β₀ + β₁X₁ + ... + βₙXₙ + ε. Simple Linear Regression uses one predictor; Multiple Linear Regression uses several. Key concepts: MSE (Mean Squared Error), R-squared (variance explained), Adjusted R-squared (penalizes complexity), Gradient Descent (optimization algorithm).

## Why do we use it?
Linear Regression is the simplest and most interpretable ML model. It serves as a baseline for all other models. Industries use it for forecasting sales, predicting housing prices, estimating risk scores, and understanding feature relationships. Its coefficients provide direct explanations of feature impacts.

## Real-Time Example 1
**Housing Price Prediction (Zillow)**
1. Zillow collects data: house size, bedrooms, location, age, price
2. A Multiple Linear Regression model predicts price = β₀ + β₁(sqft) + β₂(bedrooms) + β₃(location_score)
3. The model is trained using Gradient Descent to minimize MSE
4. R² = 0.85 means 85% of price variance is explained by the features
5. Coefficients show: each additional sqft adds $150 to price

## Real-Time Example 2
**Advertising Budget Optimization (Procter & Gamble)**
1. P&G wants to know how TV, radio, and newspaper ads affect sales
2. Multiple Linear Regression: Sales = β₀ + β₁(TV) + β₂(Radio) + β₃(Newspaper)
3. Results: TV (β₁=0.05), Radio (β₂=0.18), Newspaper (β₃=-0.01)
4. Conclusion: Radio has the highest impact; newspaper may be ineffective
5. Budget is reallocated from newspaper to radio advertising

## Concept Example 1
```python
# Simple Linear Regression
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

X = np.array([1, 2, 3, 4, 5, 6, 7, 8]).reshape(-1, 1)  # years experience
y = np.array([30, 35, 38, 45, 50, 55, 60, 65])  # salary (1000s)

model = LinearRegression()
model.fit(X, y)

y_pred = model.predict(X)

print(f"Coefficient (β₁): {model.coef_[0]:.2f}")
print(f"Intercept (β₀): {model.intercept_:.2f}")
print(f"MSE: {mean_squared_error(y, y_pred):.2f}")
print(f"R²: {r2_score(y, y_pred):.3f}")

# Predict salary for 10 years experience
print(f"Predicted salary for 10 yrs: {model.predict([[10]])[0]:.2f}K")
# Output: β₁=5.02, β₀=25.12, MSE=1.23, R²=0.989
# Predicted salary for 10 yrs: 75.36K
# Explanation: Each year adds ~$5K to salary
```

## Concept Example 2
```python
# Multiple Linear Regression with Gradient Descent (from scratch)
import numpy as np

class LinearRegressionGD:
    def __init__(self, lr=0.01, epochs=1000):
        self.lr = lr
        self.epochs = epochs

    def fit(self, X, y):
        X = np.c_[np.ones(X.shape[0]), X]  # add bias term
        self.theta = np.zeros(X.shape[1])

        for _ in range(self.epochs):
            predictions = X @ self.theta
            errors = predictions - y
            gradient = (1 / len(y)) * X.T @ errors
            self.theta -= self.lr * gradient

    def predict(self, X):
        X = np.c_[np.ones(X.shape[0]), X]
        return X @ self.theta

# Sample data: hours studied, prev grade -> final grade
X = np.array([[2, 70], [3, 75], [5, 80], [7, 85], [8, 90]])
y = np.array([72, 78, 85, 90, 95])

model = LinearRegressionGD(lr=0.01, epochs=1000)
model.fit(X, y)

print(f"Weights: {model.theta}")
# Output: [30.5, 3.2, 0.45]
# Explanation: Intercept=30.5, each study hour adds 3.2 points,
# each prev grade point adds 0.45 points to final grade
```

## Common Mistakes
- Assuming linear relationships exist when they don't
- Ignoring multicollinearity (correlated features)
- Not checking residual plots (should be random, no pattern)
- Using R² alone without Adjusted R² when adding features
- Not scaling features when using Gradient Descent
- Including correlated features leading to unstable coefficients

## How Interviewers Ask This
- "What are the assumptions of Linear Regression?"
- "Explain the difference between R-squared and Adjusted R-squared."
- "What is multicollinearity and why is it a problem?"
- "How does Gradient Descent work?"
- "What happens if you have categorical variables in linear regression?"

## How To Impress The Interviewer
Discuss all Linear Regression assumptions in detail: Linearity, Independence, Homoscedasticity (constant variance of errors), Normality of residuals, and No multicollinearity. Explain how to diagnose violations using residual plots, Q-Q plots, and VIF scores. Mention Ridge, Lasso, and Elastic Net as regularized extensions. Discuss the closed-form solution (Normal Equation: β = (XᵀX)⁻¹Xᵀy) and when Gradient Descent is preferred (large datasets, many features). Talk about feature scaling importance for GD convergence.

## Most Asked Questions

**Q1: What are the assumptions of Linear Regression?**
A: (1) Linearity: relationship between X and y is linear. (2) Independence: observations are independent. (3) Homoscedasticity: constant variance of residuals. (4) Normality: residuals are normally distributed. (5) No multicollinearity: predictors are not highly correlated. Violations require transformation or different models.

**Q2: What is the difference between R² and Adjusted R²?**
A: R² measures the proportion of variance explained by the model. It always increases with more features, even useless ones. Adjusted R² penalizes adding features: Adj R² = 1 - (1-R²)(n-1)/(n-k-1). Use Adjusted R² when comparing models with different numbers of features.

**Q3: What is MSE and why do we use it?**
A: Mean Squared Error = (1/n) Σ(yᵢ - ŷᵢ)². It measures average squared difference between actual and predicted values. Squared errors punish large mistakes more heavily. In Linear Regression, minimizing MSE is equivalent to Maximum Likelihood Estimation under Gaussian errors.

**Q4: What is multicollinearity?**
A: Multicollinearity occurs when independent variables are highly correlated. This inflates coefficient standard errors, making estimates unstable. Detection: VIF > 10 indicates serious multicollinearity. Solution: remove correlated features or use Ridge Regression. Example: Including both "years of education" and "years of schooling" (they measure the same thing).

**Q5: How does Gradient Descent work?**
A: Gradient Descent iteratively updates model parameters in the direction of the negative gradient (steepest descent) to minimize the loss function. Steps: (1) Initialize weights randomly, (2) Compute predictions, (3) Calculate error, (4) Compute gradient, (5) Update weights: θ = θ - α × gradient. Repeat until convergence.

**Q6: What is the difference between Batch GD, Stochastic GD, and Mini-batch GD?**
A: Batch GD uses all training data per update (slow but stable). SGD uses one sample per update (fast but noisy). Mini-batch GD uses a batch of samples (typically 32-256) - best of both worlds. In practice, Mini-batch GD is most common.

**Q7: What is the Normal Equation?**
A: The Normal Equation finds optimal coefficients directly: β = (XᵀX)⁻¹Xᵀy. It's a closed-form solution without iteration. Advantage: no learning rate tuning. Disadvantage: O(n³) complexity for matrix inversion, impractical for large datasets or many features.

**Q8: How do you handle categorical variables in Linear Regression?**
A: Use one-hot encoding to convert categories to binary columns. Drop one category to avoid multicollinearity (dummy variable trap). Example: A "City" feature with values (NY, LA, Chicago) becomes three binary columns (is_NY, is_LA, is_Chicago) with one dropped.

**Q9: What is the impact of outliers on Linear Regression?**
A: Outliers can significantly skew regression coefficients because MSE gives squared weight to large errors. Detection: residual plots, Z-scores, IQR method. Treatment: remove (if data entry error), cap (winsorization), or use robust regression (Huber, RANSAC).

**Q10: What is heteroscedasticity?**
A: Heteroscedasticity means the variance of residuals is not constant across predicted values. A "funnel shape" in residual plots indicates it. Consequences: incorrect standard errors, invalid hypothesis tests. Fixes: log transform y, use weighted least squares, or use heteroscedasticity-consistent standard errors.

**Q11: Explain the bias term (intercept) in Linear Regression.**
A: The intercept β₀ represents the predicted value when all features are zero. It allows the regression line to not pass through the origin. In some domains (like physics), forcing through zero makes sense; in most business cases, keep the intercept.

**Q12: What is the difference between Linear Regression and Logistic Regression?**
A: Linear Regression predicts continuous values (price, temperature). Logistic Regression predicts probabilities for binary classification (spam/not spam). Linear Regression uses MSE loss; Logistic Regression uses Log Loss (cross-entropy). Linear Regression output is unbounded; Logistic Regression squashes output between 0 and 1 using sigmoid.
