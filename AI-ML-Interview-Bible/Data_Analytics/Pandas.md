# Pandas

## What is it?
Pandas is a Python library for data manipulation and analysis. It introduces two main structures: Series (1D labeled array) and DataFrame (2D labeled table). Pandas can read/write CSV, Excel, SQL, JSON, and more. It's the foundation of data analysis in Python.

## Why do we use it?
Excel can't handle millions of rows efficiently. Pandas can. It's programmable (reproducible), integrates with machine learning, and supports complex operations (groupby, merge, pivot, time series) that are tedious in Excel. Every data scientist uses Pandas daily.

## Real-Time Example 1
**Business Scenario:** A marketing team has 2 million customer records in a CSV and needs to segment customers by spending behavior.
**Step-by-Step:**
1. `import pandas as pd`
2. `df = pd.read_csv('customers.csv')` — load 2M rows.
3. `df.info()` — check columns, dtypes, missing values.
4. `df.groupby('Segment')['Revenue'].agg(['sum', 'mean', 'count'])` — summary per segment.
5. `df['Segment'].value_counts()` — customer count per segment.
6. `df[df['Region'] == 'West'].groupby('Product')['Revenue'].sum().nlargest(10)` — top 10 products in West.
7. `df.to_excel('segments.xlsx')` — export results.

## Real-Time Example 2
**Industry:** Finance
**Use Case:** Detect fraudulent transactions in a bank's transaction log.
1. `txns = pd.read_csv('transactions.csv', parse_dates=['Date'])`
2. `txns['Amount'].describe()` — check distribution (mean, std, min, max).
3. Flag outliers: `txns['Flag'] = txns['Amount'] > txns['Amount'].quantile(0.99)`
4. Count per account: `txns.groupby('AccountID')['Flag'].sum().nlargest(20)`
5. Merge with customer data: `merged = txns.merge(customers, on='AccountID', how='left')`
6. Export suspicious accounts: `suspicious.to_csv('fraud_alert.csv', index=False)`

## Concept Example 1
**GroupBy with Aggregation**
- **Input:** DataFrame with columns: Department, Employee, Salary.
- **Code:** `df.groupby('Department')['Salary'].agg(['mean', 'sum', 'count'])`
- **Output:** A DataFrame with mean, sum, and count of salary per department.
- **Explanation:** GroupBy splits data into groups, applies aggregation functions to each, and combines results. `.agg()` allows multiple aggregations at once.

## Concept Example 2
**Handling Missing Data**
- **Input:** DataFrame with null values in Age and Salary columns.
- **Code:** `df.fillna({'Age': df['Age'].median(), 'Salary': 0})`
- **Output:** Age nulls replaced with median, Salary nulls with 0.
- **Explanation:** `fillna()` fills missing values. Methods: `ffill` (forward fill), `bfill` (backward fill), `median`, `mean`, or a constant. Always understand why data is missing before deciding.

## Common Mistakes
- Not checking `df.info()` and `df.describe()` before analysis.
- Using chained indexing (`df[df['A'] > 0]['B']`) instead of `.loc`.
- Forgetting `inplace=True` or reassigning the result.
- Confusing `axis=0` (rows) with `axis=1` (columns).
- Ignoring `dtype` issues — strings stored as objects, dates not parsed.

## How Interviewers Ask This
- "How do you handle missing values in a DataFrame?"
- "Explain the difference between loc, iloc, and ix."
- "What is the difference between merge, join, and concat?"
- "How do you apply a function to every row in a DataFrame?"

## How To Impress The Interviewer
- Discuss the difference between Eager and Lazy evaluation (Pandas vs Polars/modin).
- Show understanding of vectorization vs looping — NEVER use for loops on DataFrames.
- Mention `pd.cut()` and `pd.qcut()` for binning continuous variables.
- Know how to use `pd.pivot_table()` and `pd.melt()` for reshape operations.
- Discuss memory optimization: downcasting numeric types, using categoricals for low-cardinality strings.

## Most Asked Questions

**1. What is the difference between a Series and a DataFrame?**
A Series is a 1D labeled array (like a single column). A DataFrame is a 2D labeled table (multiple columns, each can be a Series). Series has one dtype, DataFrame can have multiple dtypes. DataFrame columns can be accessed as Series.

**2. What is the difference between loc and iloc?**
`loc` uses label-based indexing: `df.loc[0:5, ['Name', 'Age']]`. `iloc` uses integer position: `df.iloc[0:5, 0:2]`. `loc` includes the endpoint, `iloc` excludes it. `loc` accepts boolean arrays, `iloc` does not.

**3. How do you handle missing values in Pandas?**
`df.isnull().sum()` to check. `df.dropna()` to remove rows with nulls. `df.fillna(value)` to replace. Strategies: mean/median for numerical, mode for categorical, forward fill for time series, or use predictive models (IterativeImputer).

**4. What is the difference between merge, join, and concat?**
`merge` joins DataFrames on columns (SQL-like: how='inner', 'outer', 'left', 'right'). `join` joins on index. `concat` stacks DataFrames along axis (rows: `axis=0`, columns: `axis=1`). `merge` is the most flexible.

**5. How does groupby work in Pandas?**
`df.groupby('Column')['Value'].sum()` splits by unique values in Column, applies sum to Value per group, combines results. Can group by multiple columns: `groupby(['Dept', 'Year'])`. Use `.agg()` for multiple aggregations.

**6. What is the apply function used for?**
`df['Column'].apply(func)` applies a function to each element in a Series. `df.apply(func, axis=1)` applies to each row. Slower than vectorized operations. Use `np.vectorize` or list comprehensions for speed when possible.

**7. How do you read a CSV file in Pandas?**
`pd.read_csv('file.csv', sep=',', header=0, index_col=None, parse_dates=['Date'], usecols=['A','B','C'])`. Key parameters: `nrows` (sample), `chunksize` (large files), `encoding`, `dtype`, `na_values`.

**8. How do you filter a DataFrame based on conditions?**
`df[(df['Age'] > 25) & (df['City'] == 'NYC')]`. Use `|` for OR, `~` for NOT. Use `.query()` for string-based filtering: `df.query('Age > 25 and City == "NYC"')`. Use `.isin()` for multiple values: `df[df['City'].isin(['NYC', 'LA'])].

**9. What is the difference between value_counts and nunique?**
`df['Column'].value_counts()` returns counts of each unique value (frequency table). `df['Column'].nunique()` returns the number of unique values (integer). Use value_counts for distribution, nunique for cardinality.

**10. How do you rename columns in Pandas?**
`df.rename(columns={'Old': 'New'}, inplace=True)`. Or assign directly: `df.columns = ['A', 'B', 'C']`. Or add prefix/suffix: `df.add_prefix('col_')`, `df.add_suffix('_2024')`.

**11. How do you handle duplicate rows?**
`df.duplicated(subset=['Col1', 'Col2'], keep='first')` returns boolean mask. `df.drop_duplicates(subset=['Col1'], keep='first', inplace=True)` removes duplicates. `keep='first'` keeps first occurrence, `keep='last'` keeps last, `keep=False` removes all.

**12. What is the difference between map, applymap, and apply?**
`map` works on a Series (element-wise transformation with dict or function). `applymap` works on every element of a DataFrame (element-wise). `apply` works on a Series or DataFrame rows/columns. `map` is fastest, `applymap` is least used.

**13. How do you create a pivot table in Pandas?**
`pd.pivot_table(df, values='Revenue', index='Region', columns='Product', aggfunc='sum', fill_value=0, margins=True)`. Like Excel PivotTables. `melt()` is the inverse (unpivot wide to long).

**14. How do you sort a DataFrame?**
`df.sort_values(by=['Column1', 'Column2'], ascending=[True, False], inplace=True)`. `df.sort_index()` sorts by index. Use `na_position='first'` or `'last'` for null handling.

**15. What is the difference between the copy function and direct assignment?**
`df2 = df` creates a reference (changes to df2 affect df). `df2 = df.copy()` creates an independent copy. Always use `.copy()` when filtering or manipulating a DataFrame to avoid SettingWithCopyWarning.

**16. How do you detect and remove outliers in a DataFrame?**
Use IQR method: `Q1 = df['col'].quantile(0.25)`, `Q3 = df['col'].quantile(0.75)`, `IQR = Q3 - Q1`. Filter: `df[(df['col'] >= Q1-1.5*IQR) & (df['col'] <= Q3+1.5*IQR)]`. Or Z-score: `df[(np.abs(z_scores) < 3)]`.

**17. How do you work with date/time data in Pandas?**
`pd.to_datetime(df['Date'])` converts to datetime. Extract: `df['Date'].dt.year`, `.dt.month`, `.dt.dayofweek`. Set as index: `df.set_index('Date', inplace=True)`. Resample: `df.resample('M').sum()` for monthly aggregation.

**18. What is the difference between pd.cut and pd.qcut?**
`pd.cut` creates bins by specifying edges (equal-width intervals). `pd.qcut` creates bins with equal number of observations (quantile-based). Example: `pd.cut(df['Age'], bins=[0, 18, 35, 60, 120])` vs `pd.qcut(df['Income'], q=4, labels=['Q1','Q2','Q3','Q4'])`.

**19. How do you handle large DataFrames that don't fit in memory?**
(1) Use `pd.read_csv(chunksize=10000)` and process iteratively. (2) Specify `dtypes` to reduce memory. (3) Use `usecols` to load only needed columns. (4) Use Dask or Vaex for out-of-core computation. (5) Filter early with `query` parameters.

**20. How do you save a DataFrame to different formats?**
`df.to_csv('file.csv', index=False)`, `df.to_excel('file.xlsx', sheet_name='Sheet1')`, `df.to_sql('table', connection)`, `df.to_parquet('file.parquet')`, `df.to_json('file.json')`.

**21. What is the difference between bool indexing and query?**
Both filter rows: `df[df['Age'] > 30]` and `df.query('Age > 30')`. `query` is cleaner for complex conditions and slightly faster on large DataFrames because it uses numexpr. `query` cannot reference variable names with spaces without backticks.

**22. How do you create dummy/one-hot encoding in Pandas?**
`pd.get_dummies(df, columns=['Category'], prefix='cat', drop_first=True)`. Converts categorical column into binary columns. `drop_first=True` avoids multicollinearity (k-1 dummies). Alternatively, use `sklearn.preprocessing.OneHotEncoder`.

**23. How do you stack and unstack a DataFrame?**
`df.stack()` pivots columns into rows (wide to long). `df.unstack()` pivots rows into columns (long to wide). These operate on MultiIndex DataFrames. Used for reshaping hierarchical data.

**24. What is the difference between inplace operations and assignments?**
`df.dropna(inplace=True)` modifies df directly. `df = df.dropna()` creates a new DataFrame. Inplace operations return None. Inplace is slightly faster but less chainable. Most modern Pandas docs recommend assignment.

**25. How do you use the pipe method?**
`df.pipe(func)` passes the DataFrame to a function. Used for chaining custom transformations: `df.pipe(clean).pipe(transform).pipe(aggregate)`. Cleaner than nested function calls.
