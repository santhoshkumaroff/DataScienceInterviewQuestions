# Seaborn

## What is it?
Seaborn is a Python visualization library built on top of Matplotlib. It provides a high-level interface for creating statistical graphics. With one line of code, you get heatmaps, pair plots, box plots, violin plots, and distribution plots that would take many lines in Matplotlib.

## Why do we use it?
Seaborn makes beautiful statistical plots with minimal code. It automatically handles legends, color palettes, and axis labels. It's the go-to library for exploratory data analysis (EDA) because it quickly reveals patterns, distributions, and relationships.

## Real-Time Example 1
**Business Scenario:** An e-commerce company wants to understand customer behavior patterns through visual EDA.
**Step-by-Step:**
1. `import seaborn as sns`
2. `sns.set_theme(style='whitegrid', palette='muted')` — set professional style.
3. `sns.histplot(data=df, x='PurchaseAmount', bins=50, kde=True)` — distribution of spending.
4. `sns.boxplot(data=df, x='CustomerSegment', y='PurchaseAmount')` — compare segments.
5. `sns.heatmap(df.corr(numeric_only=True), annot=True, cmap='coolwarm')` — correlation matrix.
6. `sns.pairplot(df, vars=['Age', 'Income', 'Spend', 'Frequency'], hue='Churned')` — multi-dimension view.
7. All plots reveal insights: segment differences, correlations, churn patterns.

## Real-Time Example 2
**Industry:** HR / People Analytics
**Use Case:** Analyze employee satisfaction survey data across departments.
1. `sns.countplot(data=df, x='Department', hue='SatisfactionLevel')` — counts per department.
2. `sns.violinplot(data=df, x='Department', y='YearsAtCompany', hue='Attrition')` — distribution of tenure.
3. `sns.pointplot(data=df, x='YearsAtCompany', y='Satisfaction', ci='sd')` — trend with confidence.
4. `sns.lmplot(data=df, x='YearsAtCompany', y='Salary', hue='Attrition', col='Department')` — regression per department.
5. Result: Identify departments with low satisfaction, high attrition risk, and salary disparities.

## Concept Example 1
**Heatmap for Correlation Analysis**
- **Code:** `sns.heatmap(df.corr(numeric_only=True), annot=True, fmt='.2f', cmap='RdBu_r', center=0, square=True)`
- **Output:** A color-coded matrix showing correlations between all numeric columns.
- **Explanation:** `annot=True` shows correlation values. `cmap='RdBu_r'` uses red-blue (blue = positive, red = negative). `center=0` ensures white = zero correlation. `square=True` makes cells square. Essential for feature selection in ML.

## Concept Example 2
**Pairplot for Multivariate Exploration**
- **Code:** `sns.pairplot(df, hue='Segment', diag_kind='kde', palette='Set2', markers=['o', 's', 'D'])`
- **Output:** A grid of scatter plots (off-diagonal) and KDE plots (diagonal) colored by Segment.
- **Explanation:** Pairplot shows every numeric column plotted against every other. `hue` colors by category. `diag_kind='kde'` shows density instead of histogram. Reveals clusters, separations, and relationships at a glance.

## Common Mistakes
- Using Seaborn on un-cleaned data (outliers skew distributions).
- Forgetting `sns.set_theme()` for consistent styling.
- Using `sns.plt.show()` (should be `plt.show()` from Matplotlib).
- Using pairplot on high-dimensional data (too many plots, small visibility).
- Not understanding that Seaborn works best with long-format (tidy) data.

## How Interviewers Ask This
- "How do you create a heatmap of correlations in Seaborn?"
- "What is the difference between a boxplot and a violinplot?"
- "How would you visualize the relationship between multiple numerical variables?"
- "How do you change the Seaborn theme or style?"

## How To Impress The Interviewer
- Mention `sns.jointplot` with `kind='hex'` or `kind='kde'` for dense scatter data.
- Discuss `sns.clustermap` for hierarchical clustering heatmaps.
- Show knowledge of `sns.FacetGrid` for creating multi-panel plots by category.
- Use `sns.set_context('paper')`, `'notebook'`, `'talk'`, or `'poster'` for appropriate scaling.
- Customize with `sns.set_palette('husl', 8)` for perceptually uniform discrete colors.

## Most Asked Questions

**1. What is Seaborn and how is it different from Matplotlib?**
Seaborn is a higher-level statistical visualization library built on Matplotlib. It provides default styles, color palettes, and statistical transforms. Seaborn automatically calculates aggregations, confidence intervals, and distributions. Matplotlib requires more code for the same output.

**2. How do you set the default style in Seaborn?**
`sns.set_theme(style='darkgrid', palette='muted', font='sans-serif', font_scale=1.2)`. Options: 'darkgrid', 'whitegrid', 'dark', 'white', 'ticks'. `sns.set_context('talk')` adjusts scaling for different presentation formats.

**3. What is a heatmap and how do you create one?**
A heatmap represents values in a matrix with colors. `sns.heatmap(data, annot=True, cmap='coolwarm', fmt='.1f')`. Commonly used for correlation matrices, confusion matrices, pivot tables, and time-series matrices.

**4. What is a pairplot and when would you use it?**
Pairplot creates a grid of scatter plots for all numerical variable pairs with distribution plots on the diagonal. Use it for: exploratory analysis, identifying correlations, spotting clusters, and checking for separation by categories.

**5. What is the difference between a boxplot and a violinplot?**
A boxplot shows quartiles, median, and outliers (box + whiskers). A violinplot adds a kernel density estimate on each side, showing the full distribution shape. Violinplots reveal multimodality (multiple peaks) that boxplots hide. Use boxplots for simplicity, violinplots for detail.

**6. How do you create a boxplot in Seaborn?**
`sns.boxplot(data=df, x='Category', y='Value', hue='SubCategory', palette='Set3')`. `hue` splits each category by a second variable. `order` controls category order. `notch=True` shows confidence intervals.

**7. What is a countplot and how is it different from a barplot?**
A countplot shows the count of observations in each category: `sns.countplot(data=df, x='Category')`. A barplot (`sns.barplot`) shows an aggregation (default: mean) of a numerical variable per category. Countplot = frequency, Barplot = aggregated statistic.

**8. How do you create a distribution plot?**
`sns.histplot(data=df, x='Value', bins=30, kde=True)` — modern approach (replaces distplot). `sns.kdeplot(data=df, x='Value', fill=True)` for smooth density only. `sns.ecdfplot(data=df, x='Value')` for cumulative distribution.

**9. What is a jointplot and what kinds are available?**
`sns.jointplot(data=df, x='Height', y='Weight', kind='scatter')`. Kinds: 'scatter' (default), 'kde' (density contours), 'hex' (hexbin for large data), 'reg' (scatter + regression line), 'hist' (bivariate histogram). Shows joint distribution with marginal distributions.

**10. How do you create a FacetGrid (multi-panel plots by category)?**
```python
g = sns.FacetGrid(df, col='Region', row='Segment', hue='Gender')
g.map(sns.scatterplot, 'Age', 'Income')
g.add_legend()
```
FacetGrid creates subplots for each combination of categorical variables. Automatically shares axes for comparison. `col_wrap` controls number of columns.

**11. What is a catplot and when would you use it?**
`sns.catplot(data=df, x='Category', y='Value', hue='SubCat', kind='box', col='Region')`. Catplot is a figure-level function that combines boxplot, violinplot, boxenplot, pointplot, barplot, strip plot, or swarm plot with FacetGrid. Use for all categorical-numerical relationships.

**12. How do you create a regression plot?**
`sns.regplot(data=df, x='X', y='Y')` — scatter plot with regression line and confidence band. `sns.lmplot(data=df, x='X', y='Y', hue='Category', col='Col')` — figure-level version with FacetGrid. `order=2` for polynomial, `logistic=True` for logistic regression.

**13. How do you customize colors in Seaborn?**
`sns.set_palette('husl', 10)` sets global palette. `sns.color_palette('Blues', 5)` creates a palette object. `sns.palplot(sns.color_palette('viridis', 8))` visualizes. Options: 'deep', 'muted', 'pastel', 'bright', 'dark', 'colorblind', or any Matplotlib colormap.

**14. How do you save a Seaborn plot?**
Seaborn uses Matplotlib under the hood. Save via: `plt.savefig('plot.png', dpi=300, bbox_inches='tight')`. Call after `sns.heatmap(...)` but before `plt.show()`. Or assign to `ax` and call `ax.figure.savefig(...)`.

**15. What is a clustermap?**
`sns.clustermap(data, z_score=1, cmap='vlag', col_cluster=True, row_cluster=True)`. A heatmap with hierarchical clustering applied to rows and columns. Used for: gene expression analysis, finding similar groups in data, and identifying patterns.

**16. What is a pairplot?**
See above. `sns.pairplot(df, hue='Target', vars=['col1','col2','col3'], corner=True)`. `corner=True` shows only lower triangle (less clutter). Can specify `vars` to limit to specific columns. Use `kind='kde'` for large datasets.

**17. How do you handle large datasets in Seaborn?**
(1) Use `sns.kdeplot` or `sns.ecdfplot` instead of `sns.histplot` for smaller file sizes. (2) Use `sns.jointplot(kind='hex')` for scatter data. (3) Sample the data: `df.sample(10000)`. (4) Use `alpha=0.1` for scatter transparency. (5) Use `rasterized=True` for vector exports.

**18. What is the difference between figure-level and axes-level functions?**
Figure-level functions (relplot, displot, catplot, lmplot) create a new figure with FacetGrid support. Axes-level functions (scatterplot, histplot, boxplot, regplot) draw on existing axes. Figure-level functions are more flexible for multi-panel plots.

**19. How do you annotate a heatmap?**
`sns.heatmap(data, annot=True, fmt='.2f', annot_kws={'size': 10})`. `annot` can be a DataFrame (for custom labels). `fmt` formats numbers. `annot_kws` controls text styling. For triangle matrices, create a mask: `mask = np.triu(np.ones_like(data, dtype=bool))`.

**20. How do you create a barplot with error bars?**
`sns.barplot(data=df, x='Category', y='Value', ci=95, capsize=0.1)`. `ci` (confidence interval) can be: percentage (95), 'sd' (standard deviation), or None. `estimator=np.median` changes aggregation. `errwidth` and `capsize` control error bar appearance.

**21. What is a pointplot?**
`sns.pointplot(data=df, x='Month', y='Sales', hue='Region')`. Shows point estimates (mean) with error bars connected by lines. Useful for trend visualization across categories. Alternative to barplot when showing trends is more important than individual values.

**22. How do you create a strip plot or swarm plot?**
`sns.stripplot(data=df, x='Category', y='Value', jitter=True)` — shows individual points with jitter. `sns.swarmplot(data=df, x='Category', y='Value')` — non-overlapping points arranged to show distribution. Swarm plots are better but slow with large data.

**23. How do you use hue, col, and row parameters?**
`hue`: color points by a categorical variable. `col`: create separate subplots for each value (columns). `row`: create subplots by rows. Example: `sns.lmplot(data=df, x='Age', y='Income', hue='Gender', col='Region', row='Education')`.

**24. How do you create a cumulative distribution plot?**
`sns.ecdfplot(data=df, x='Value', hue='Category')` shows the empirical cumulative distribution function. X-axis = value, Y-axis = proportion of data ≤ that value. Useful for: comparing distributions, finding percentiles, and checking for skewness.

**25. How do you export a Seaborn figure as high-quality PDF?**
```python
sns.set_theme(style='whitegrid', font_scale=1.5)
fig = sns.displot(data=df, x='Value', kde=True, height=6, aspect=1.5)
fig.savefig('output.pdf', dpi=300, bbox_inches='tight')
```
Use `fig.savefig` for figure-level functions. Use `plt.savefig` with `dpi=300` for high-resolution. PDF format preserves vector graphics for publication.
