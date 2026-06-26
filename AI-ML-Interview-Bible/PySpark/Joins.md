# Joins

## What is it?
Joins in Spark combine two DataFrames based on a common key or condition. Spark supports inner, outer, left, right, semi, anti joins. It chooses between shuffle join (sort-merge or hash) and broadcast join based on data size. Broadcast join is optimized for joining a large table with a small one.

## Why do we use it?
Joins enable combining data from multiple sources (users + transactions, sales + products). Understanding join optimization is critical for performance — a poorly chosen join strategy can cause hours of execution time vs minutes.

## Real-Time Example 1
**E-Commerce Order Enrichment:** Amazon enriches orders with product details.
1. Orders DataFrame (500M rows, large) — order_id, product_id, quantity.
2. Products DataFrame (5M rows, medium) — product_id, name, category, price.
3. Spark broadcasts the smaller products table to all executors.
4. Broadcast hash join runs entirely in memory — no shuffle.
5. Enriched results are saved for recommendation engine.

## Real-Time Example 2
**Banking Fraud Detection:** A bank joins transaction data with account blacklists.
1. Transactions DataFrame (2B rows) — txn_id, account_id, amount, timestamp.
2. Blacklist DataFrame (10K rows) — account_id, reason.
3. Since blacklist is tiny, Spark uses broadcast join.
4. Matched transactions are flagged for review.
5. The join completes in <1 minute on 100TB of data.

## Concept Example 1
**Different join types with examples.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("JoinTypes").getOrCreate()

orders = spark.createDataFrame([
    (1, "Alice", 100), (2, "Bob", 200), (3, "Charlie", 150)
], ["order_id", "customer", "amount"])

payments = spark.createDataFrame([
    (1, "Paid"), (2, "Pending"), (4, "Paid")
], ["order_id", "status"])

# Inner join
orders.join(payments, "order_id").show()
# +--------+--------+------+------+
# |order_id|customer|amount|status|
# +--------+--------+------+------+
# |       1|   Alice|   100|  Paid|
# |       2|     Bob|   200|Pending|
# +--------+--------+------+------+

# Left outer join
orders.join(payments, "order_id", "left").show()
# +--------+--------+------+-------+
# |order_id|customer|amount| status|
# +--------+--------+------+-------+
# |       1|   Alice|   100|   Paid|
# |       2|     Bob|   200|Pending|
# |       3| Charlie|   150|   null|
# +--------+--------+------+-------+

# Anti join (only non-matching)
orders.join(payments, "order_id", "anti").show()
# +--------+--------+------+
# |order_id|customer|amount|
# +--------+--------+------+
# |       3| Charlie|   150|
# +--------+--------+------+
```

**Input:** Two DataFrames with order_id key.
**Output:** Joined results based on join type.
**Explanation:** Inner returns only matching keys. Left keeps all left-side rows. Anti keeps only rows with no match — useful for finding orphans.

## Concept Example 2
**Broadcast join vs shuffle join.**

```python
from pyspark.sql import SparkSession
import pyspark.sql.functions as F

spark = SparkSession.builder.appName("BroadcastJoin").getOrCreate()

# Large table (1B rows)
transactions = spark.read.parquet("/data/transactions/")

# Small table (10K rows)
users = spark.read.parquet("/data/users/").select("user_id", "segment")

# Option 1: Broadcast hint (recommended for small table)
from pyspark.sql.functions import broadcast
result = transactions.join(broadcast(users), "user_id")

# Option 2: Auto broadcast (Spark decides based on stats)
result = transactions.join(users, "user_id")

# Check the physical plan
result.explain()
```

**Input:** Large transactions + small users table.
**Output:** Transactions enriched with user segment.
**Explanation:** `broadcast()` hints Spark to send the users table to all executors. Without the hint, Spark estimates sizes. If users < `spark.sql.autoBroadcastJoinThreshold` (default 10MB), it auto-broadcasts. The plan shows `BroadcastHashJoin` instead of `SortMergeJoin`.

## Common Mistakes
- Joining a large table with another large table without filtering first.
- Not broadcasting a small lookup table when it fits in memory.
- Using `cross join` accidentally (Cartesian product) — extremely expensive.
- Joining on columns with null keys (null != null in Spark — rows are dropped).
- Not specifying the join key properly — ambiguous column references.

## How Interviewers Ask This
- "Explain broadcast join vs sort-merge join."
- "How does Spark choose a join strategy?"
- "What happens when both tables are large?"
- "How do you optimize joins in PySpark?"
- "What is a skew join and how do you handle it?"

## How To Impress The Interviewer
- Explain the three join strategies: Broadcast Hash Join (small table), Sort-Merge Join (both large, sorted), Shuffled Hash Join (one side small enough to build hash table).
- Discuss join hints: `broadcast`, `merge`, `shuffle_hash`, `shuffle_replicate_nl`.
- For data skew, mention salting: add a random prefix to skewed keys, join in two phases.
- Explain that `sort-merge-join` requires sorting both sides — expensive but handles large data.
- Mention that Spark 3.x introduces Adaptive Query Execution (AQE) — it can convert sort-merge to broadcast join at runtime based on statistics.

## Most Asked Questions

**1. What join types does Spark support?**
Inner, Cross, Left Outer, Right Outer, Full Outer, Left Semi, Left Anti. Use `.join(df2, "key")` or `.join(df2, on="key", how="inner")`.

**2. What is a broadcast join?**
Spark sends the smaller DataFrame to all executors. Each executor performs hash join locally — no shuffle. Only works when one side fits in memory.

**3. When does Spark use a Sort-Merge join?**
When both DataFrames are large and not suitable for broadcasting. Spark sorts both sides by the join key, then merges. Default strategy for large joins.

**4. How do you set the broadcast threshold?**
`spark.sql.autoBroadcastJoinThreshold` — default 10MB. Can be increased if memory allows. Use `broadcast()` hint explicitly for important cases.

**5. What is a skew join?**
When one key has significantly more rows than others. One executor handles all rows for that key — becomes a bottleneck.

**6. How do you handle data skew in joins?**
Salt the skewed key: add a random suffix to the skewed key, replicate the small table N times, join in two phases — first with salted keys, then aggregate.

**7. What is the difference between ShuffledHashJoin and SortMergeJoin?**
ShuffledHashJoin: Builds a hash table from one side (faster but memory intensive). SortMergeJoin: Sorts both sides then merges (handles larger data, uses disk).

**8. What is a self-join?**
Joining a DataFrame with itself. Use `.alias()` to disambiguate columns. Example: `df.alias("a").join(df.alias("b"), "col")`.

**9. What is a cross join?**
Cartesian product — every row of df1 combined with every row of df2. Dangerous for large data. Must explicitly set `spark.sql.crossJoin.enabled=true`.

**10. How do you check the join plan?**
`df.explain()` — shows whether Spark uses BroadcastHashJoin or SortMergeJoin or ShuffledHashJoin.

**11. What is the impact of null keys on joins?**
Null keys do not match in joins. Rows with null keys are dropped (except anti/semi joins, which treat nulls differently).

**12. How do you join DataFrames with different column names?**
`.join(df2, df1.col1 == df2.col2, "inner")` — specify the condition explicitly.

**13. What is AQE (Adaptive Query Execution) and how does it help joins?**
AQE in Spark 3.x can dynamically switch join strategies at runtime. If it detects one side is small enough, it converts SortMergeJoin to BroadcastHashJoin.

**14. What is bucket join?**
If both DataFrames are bucketed by the join key with the same number of buckets, Spark can avoid shuffle entirely. `CLUSTERED BY (key) INTO 64 BUCKETS`.

**15. What is the `joinWith` operation?**
`df1.joinWith(df2, condition)` — returns a DataFrame of paired tuples. Used with structured types (e.g., case classes in Scala).
