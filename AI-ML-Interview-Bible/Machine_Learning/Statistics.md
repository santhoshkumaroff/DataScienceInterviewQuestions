# Statistics for Machine Learning

## What is it?
Statistics is the science of collecting, analyzing, and interpreting data. Key concepts for ML include: Mean (average), Median (middle value), Mode (most frequent), Variance (spread of data), Standard Deviation (sqrt of variance), Normal Distribution (bell curve), Central Limit Theorem (sample means approach normal), Hypothesis Testing (making data-driven decisions), p-value (evidence against null hypothesis), and Confidence Intervals (range of plausible values).

## Why do we use it?
Statistics provides the mathematical foundation for ML. Understanding data distributions helps choose the right model. Hypothesis testing validates experiments (A/B testing). Confidence intervals quantify prediction uncertainty. Every ML practitioner needs stats to debug models, interpret results, and communicate findings.

## Real-Time Example 1
**A/B Testing at Netflix**
1. Netflix wants to test a new recommendation algorithm
2. Users are split into Control (old algorithm) and Treatment (new algorithm)
3. Null hypothesis: No difference in watch time between groups
4. After collecting data, a t-test computes the p-value
5. If p < 0.05, they reject the null and deploy the new algorithm

## Real-Time Example 2
**Quality Control at Toyota**
1. Toyota measures the diameter of 10,000 car tires
2. They compute mean = 50cm, standard deviation = 0.1cm
3. Using the Normal Distribution, they flag any tire outside 3 standard deviations (99.7% rule)
4. A tire measuring 50.4cm is flagged as defective (6 sigma away)
5. The Central Limit Theorem ensures their sampling approach is valid

## Concept Example 1
```python
# Mean, Median, Mode, Variance, Standard Deviation
import numpy as np
from scipy import stats

data = [12, 15, 14, 10, 18, 15, 20, 15, 13, 17]

mean = np.mean(data)
median = np.median(data)
mode = stats.mode(data, keepdims=True)
variance = np.var(data, ddof=1)  # sample variance
std_dev = np.std(data, ddof=1)

print(f"Mean: {mean:.2f}")
print(f"Median: {median:.2f}")
print(f"Mode: {mode.mode[0]} (count: {mode.count[0]})")
print(f"Variance: {variance:.2f}")
print(f"Std Dev: {std_dev:.2f}")
# Output: Mean: 14.90, Median: 15.00, Mode: 15, Variance: 8.77, Std Dev: 2.96
# Explanation: Most values cluster around 15, with moderate spread
```

## Concept Example 2
```python
# Central Limit Theorem Demonstration
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(42)
population = np.random.exponential(scale=5, size=100000)  # non-normal

sample_means = []
for _ in range(1000):
    sample = np.random.choice(population, size=30)
    sample_means.append(np.mean(sample))

print(f"Population mean: {np.mean(population):.2f}")
print(f"Mean of sample means: {np.mean(sample_means):.2f}")
print(f"Population std: {np.std(population):.2f}")
print(f"Std of sample means: {np.std(sample_means):.2f}")
# Output: Population mean: 5.00, Mean of sample means: 5.01
# Explanation: Even though population is exponential, sample means form a normal distribution
```

## Common Mistakes
- Confusing p-value with the probability that the null hypothesis is true
- Using mean when median is more appropriate (skewed data)
- Assuming data is normally distributed without checking
- Using standard deviation without understanding the underlying distribution
- Drawing causal conclusions from correlation

## How Interviewers Ask This
- "What is the difference between mean and median? When would you use each?"
- "Explain the Central Limit Theorem and why it matters for ML."
- "What is a p-value? How do you interpret p = 0.03?"
- "How do you detect if a feature follows a normal distribution?"
- "What is the 68-95-99.7 rule?"

## How To Impress The Interviewer
Discuss the relationship between p-values and confidence intervals. Explain that in ML, we often skip hypothesis testing in favor of effect size and practical significance. Mention that Bayesian statistics offers an alternative to frequentist hypothesis testing. Talk about how bootstrapping can estimate confidence intervals without normality assumptions. Show awareness of multiple testing correction (Bonferroni, FDR) when running many experiments.

## Most Asked Questions

**Q1: What is the difference between mean, median, and mode?**
A: Mean is the average (sum/n). Median is the middle value when sorted. Mode is the most frequent value. For symmetric data, mean ≈ median. For skewed data (like income), median is more representative. Example: In housing prices with a few mansions, median price is more useful than mean.

**Q2: What is variance and standard deviation?**
A: Variance measures how spread out data is from the mean (average squared deviation). Standard deviation is the square root of variance, in the same units as the data. Example: Heights of adult men have std dev ≈ 3 inches - most men are within 6 inches of the average.

**Q3: What is the Normal Distribution?**
A: A symmetric, bell-shaped distribution defined by mean and standard deviation. About 68% of data falls within 1 std dev, 95% within 2, and 99.7% within 3. Many natural phenomena follow it (heights, IQ scores, measurement errors).

**Q4: Explain the Central Limit Theorem (CLT).**
A: The CLT states that the distribution of sample means approaches a normal distribution as sample size increases, regardless of the population's distribution. This is why we can use normal-based statistics even on non-normal data. Example: The average of 30 dice rolls will be approximately normal, even though individual rolls are uniform.

**Q5: What is hypothesis testing?**
A: Hypothesis testing evaluates whether an observed effect is statistically significant. Steps: (1) State null (H0) and alternative (H1) hypotheses, (2) Choose significance level (α=0.05), (3) Compute test statistic, (4) Calculate p-value, (5) Reject H0 if p < α. Example: Testing if a new drug lowers blood pressure more than placebo.

**Q6: What is a p-value?**
A: The p-value is the probability of observing results as extreme as (or more extreme than) the actual data, assuming the null hypothesis is true. A p-value of 0.03 means there's a 3% chance of seeing this result if H0 is true. It is NOT the probability that H0 is true.

**Q7: What is a confidence interval?**
A: A confidence interval provides a range of plausible values for a population parameter. A 95% CI means: if we repeated the experiment 100 times, 95 intervals would contain the true parameter. Example: "The average height is 170cm with 95% CI [168, 172]" means we're 95% confident the true mean lies between 168 and 172.

**Q8: When would you use a t-test vs a z-test?**
A: Use a t-test when the population standard deviation is unknown (use sample std dev). Use a z-test when population std dev is known and sample size is large (n > 30). In practice, t-tests are more common because we rarely know the population std dev.

**Q9: What is correlation vs causation?**
A: Correlation measures the strength of linear relationship between two variables. Causation means one variable directly affects another. Correlation does not imply causation. Example: Ice cream sales and drowning deaths are correlated (both increase in summer), but ice cream does not cause drowning.

**Q10: What is the difference between descriptive and inferential statistics?**
A: Descriptive statistics summarize data (mean, median, std dev, charts). Inferential statistics use sample data to make conclusions about populations (hypothesis testing, confidence intervals, regression). Example: "The average height of 100 sampled students is 165cm" is descriptive. "The population average height is between 163-167cm" is inferential.

**Q11: What is a Z-score and how is it used?**
A: A Z-score measures how many standard deviations a data point is from the mean. Z = (x - μ) / σ. Used for outlier detection and standardization. Example: A test score of 85 with mean 70 and std 10 has Z = 1.5 (1.5 std devs above average).

**Q12: What is the difference between Type I and Type II errors?**
A: Type I error (false positive): rejecting H0 when it's true. Type II error (false negative): failing to reject H0 when it's false. α controls Type I, β controls Type II, and 1-β is statistical power. Example: Type I = convicting an innocent person, Type II = letting a guilty person go free.
