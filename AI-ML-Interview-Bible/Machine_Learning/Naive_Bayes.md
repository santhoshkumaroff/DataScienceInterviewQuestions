# Naive Bayes

## What is it?
Naive Bayes is a probabilistic classifier based on Bayes Theorem with a "naive" assumption that features are conditionally independent given the class. Despite this simplification, it works well for many real-world problems. Common types: Gaussian NB (continuous features follow normal distribution), Multinomial NB (count data, text classification), and Bernoulli NB (binary features).

## Why do we use it?
Naive Bayes is extremely fast, scales well with high-dimensional data, and requires little training data. It's the standard baseline for text classification. Used in: spam filtering, sentiment analysis, document categorization, and recommendation systems. Facebook uses it for language detection.

## Real-Time Example 1
**Sentiment Analysis (Twitter/X)**
1. Twitter preprocesses tweets: remove punctuation, convert to lowercase
2. Each tweet becomes a bag-of-words vector (word frequencies)
3. Multinomial Naive Bayes calculates P(Sentiment | Words) for each class
4. P(Positive | "love", "great", "amazing") is computed using word probabilities
5. The class with highest probability is predicted as the sentiment

## Real-Time Example 2
**News Categorization (Google News)**
1. News articles are converted to TF-IDF feature vectors
2. Naive Bayes predicts category: Sports, Politics, Tech, Business
3. P(Sports | "goal", "match", "score") is high because these words are frequent in Sports
4. The naive independence assumption simplifies computation
5. Google categorizes thousands of articles per second

## Concept Example 1
```python
# Gaussian Naive Bayes on Iris Dataset
from sklearn.naive_bayes import GaussianNB
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, confusion_matrix
from sklearn.datasets import load_iris
import numpy as np

data = load_iris()
X, y = data.data, data.target

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

model = GaussianNB()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print(f"Accuracy: {accuracy_score(y_test, y_pred):.3f}")
print(f"Confusion Matrix:\n{confusion_matrix(y_test, y_pred)}")
print(f"Class priors: {model.class_prior_}")
# Output:
# Accuracy: 1.000
# Confusion Matrix:
# [[10  0  0]
#  [ 0  9  0]
#  [ 0  0 11]]
# Explanation: Perfect classification on Iris test set
```

## Concept Example 2
```python
# Multinomial Naive Bayes for Text Classification
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import Pipeline

# Simple text data
texts = [
    "free money click here",        # spam
    "win a prize now",              # spam
    "congratulations you won",      # spam
    "meeting at 3pm",               # ham
    "project deadline tomorrow",    # ham
    "lunch at noon",                # ham
]
labels = [1, 1, 1, 0, 0, 0]

new_emails = ["free prize click", "project meeting tomorrow", "win money now"]

# Create pipeline
pipeline = Pipeline([
    ('vectorizer', CountVectorizer()),
    ('classifier', MultinomialNB())
])

pipeline.fit(texts, labels)
preds = pipeline.predict(new_emails)

for email, pred in zip(new_emails, preds):
    print(f"'{email}' -> {'Spam' if pred == 1 else 'Ham'}")

# Output:
# 'free prize click' -> Spam
# 'project meeting tomorrow' -> Ham
# 'win money now' -> Spam
# Explanation: Words like "free", "prize", "money" are associated with spam
```

## Common Mistakes
- Assuming Naive Bayes' independence assumption always hurts performance (often it doesn't)
- Using Gaussian NB for text data (use Multinomial or Bernoulli)
- Not handling unseen words in test data (Laplace smoothing handles this)
- Using NB when features are highly correlated (it violates the assumption)
- Expecting calibrated probabilities from Naive Bayes (they are often extreme)

## How Interviewers Ask This
- "Why is it called 'Naive' Bayes?"
- "Explain the difference between Gaussian, Multinomial, and Bernoulli Naive Bayes."
- "What is Laplace Smoothing and why is it needed?"
- "How does Naive Bayes handle unseen words?"
- "What happens if features are correlated in Naive Bayes?"

## How To Impress The Interviewer
Explain that despite the naive assumption, Naive Bayes often performs well because the assumption can be "broken" in ways that don't affect ranking (correct class still gets highest probability). Discuss the log-probability trick: using logarithms avoids underflow when multiplying many small probabilities. Explain that Multinomial NB with TF-IDF often outperforms raw counts. Discuss how Naive Bayes is a generative model (models P(X,Y)) while Logistic Regression is discriminative (models P(Y|X)). Mention that with sufficient data, discriminative models outperform generative, but NB excels with small data.

## Most Asked Questions

**Q1: Why is it called "Naive" Bayes?**
A: Because it makes a naive assumption: all features are conditionally independent given the class. In reality, features are often correlated (e.g., words "congratulations" and "prize" often appear together), but the algorithm still works well despite this simplification.

**Q2: What is the difference between Gaussian, Multinomial, and Bernoulli Naive Bayes?**
A: Gaussian NB assumes continuous features follow a normal distribution (use for real-valued features like height, temperature). Multinomial NB assumes features are counts (use for text classification with word counts). Bernoulli NB assumes binary features (use for text with word presence/absence, like "contains word?").

**Q3: What is Laplace Smoothing?**
A: Laplace Smoothing (add-1 smoothing) adds 1 to every feature count to avoid zero probabilities. If a word in the test set was never seen in training, its probability would be 0, zeroing out the entire posterior. Smoothing prevents this. Formula: P(word|class) = (count + 1) / (total + |vocabulary|).

**Q4: How does Naive Bayes handle continuous features?**
A: Continuous features can be (1) discretized into bins, or (2) modeled with a probability distribution (Gaussian NB assumes normal distribution). Gaussian NB estimates mean and variance of each feature per class from training data and computes probability using the normal PDF.

**Q5: What are the pros and cons of Naive Bayes?**
A: Pros: fast training and prediction, works with high-dimensional data, handles small datasets well, robust to irrelevant features. Cons: assumes feature independence (often violated), provides poor probability estimates, sensitive to zero probabilities (solved by smoothing).

**Q6: Why is Naive Bayes considered a generative model?**
A: Generative models learn P(X, Y) = P(Y) × P(X|Y) and can generate new data points. Naive Bayes learns the probability distribution of features given each class. Discriminative models (like Logistic Regression) learn P(Y|X) directly - they can only classify, not generate.

**Q7: How does Naive Bayes perform compared to Logistic Regression?**
A: With enough data, Logistic Regression usually outperforms Naive Bayes because it doesn't assume feature independence. With small data, Naive Bayes often wins because it has lower variance. When features are truly independent, they are equivalent. In practice, try both.

**Q8: What is the "zero-frequency problem" in Naive Bayes?**
A: If a feature value never appears in training for a given class, its conditional probability is 0, making the entire posterior probability 0. Example: The word "algorithm" never appeared in spam training data, so P("algorithm"|spam) = 0. Laplace Smoothing solves this.

**Q9: Can Naive Bayes handle missing values?**
A: At training time, Naive Bayes usually requires complete data (no missing values). Some implementations ignore missing values during probability calculation. In practice, impute missing values before training or use a model variant that handles missingness.

**Q10: How do you use Naive Bayes for multi-class classification?**
A: Naive Bayes naturally handles multiple classes by computing P(Class_k | Features) for each class k and selecting the maximum. Unlike SVM (which uses one-vs-one or one-vs-rest), no special wrapping is needed. It works well for problems with many classes like document categorization.

**Q11: What is the relationship between Naive Bayes and Bayesian Networks?**
A: Naive Bayes is the simplest form of Bayesian Network with a structure where all features are children of the class variable and are independent given the class. Bayesian Networks can represent more complex dependencies (e.g., tree-augmented NB), at the cost of higher complexity.

**Q12: How do you handle imbalanced data with Naive Bayes?**
A: Naive Bayes uses class priors P(Y) from training data. If classes are imbalanced, these priors bias predictions toward the majority class. Solutions: (1) Set uniform class priors P(Y) = 1/K, (2) Oversample minority class, (3) Adjust decision threshold, (4) Use complement Naive Bayes for text data.
