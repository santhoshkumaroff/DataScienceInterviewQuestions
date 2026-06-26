# Transformations and Actions

## What is it?
Transformations create new DataFrames/RDDs from existing ones (lazy evaluation — computed only when an action runs). Actions trigger actual computation and return results or write data. Narrow transformations operate within a single partition; wide transformations require shuffling data across partitions.

## Why do we use it?
Lazy evaluation allows Spark to build an optimized DAG before executing. Spark applies optimizations like predicate pushdown, pipelining, and code generation. Separation of transformations and actions gives Spark the intelligence to minimize data movement.

## Real-Time Example 1
**ETL Pipeline for Clickstream Data:** A media company processes 10TB of daily click logs.
1. Transformations: Read CSV → filter bots → parse URLs → enrich with geo-IP → map to schema.
2. All transformations are lazy — Spark just builds a DAG.
3. Action: `df.write.parquet("/clean/clickstream/")` triggers execution.
4. Spark pipelines narrow transforms (filter → map → filter) into one stage.
5. Wide transforms (groupBy, join) create stage boundaries with shuffles.

## Real-Time Example 2
**Real-Time Ad Bidding Analysis:** An ad-tech company processes bid requests.
1. Read from Kafka stream → parse JSON → filter mobile traffic → extract features.
2. Transformations are chained declaratively.
3. Action `foreachBatch` writes results to a dashboard store.
4. Spark optimizes the execution plan — pushes filters closer to the source.

## Concept Example 1
**Narrow vs Wide transformations comparison.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("NarrowWide").getOrCreate()

df = spark.read.parquet("/data/events/")

# Narrow transformations (no shuffle)
filtered = df.filter(df.event_type == "purchase")         # narrow
enriched = filtered.withColumn("year", df.year)            # narrow

# Wide transformation (shuffle — stage boundary)
grouped = enriched.groupBy("year").count()                 # wide

# Action — triggers everything
grouped.show()

# Check the plan
grouped.explain()
```

**Input:** Parquet file of events with event_type and year columns.
**Output:** Count of purchase events per year.
**Explanation:** `filter` and `withColumn` are narrow — each partition processes independently. `groupBy` is wide — requires shuffle. `explain()` shows the physical plan with `Exchange` (shuffle) at the stage boundary.

## Concept Example 2
**Common actions and their behavior.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ActionsDemo").getOrCreate()

df = spark.range(1, 1000000).repartition(10)

# Action 1: count
print(f"Count: {df.count()}")                        # 999999

# Action 2: collect — brings ALL data to driver (dangerous!)
data = df.collect()                                   # Returns list of Rows

# Action 3: take — brings N rows
sample = df.take(5)                                   # Returns first 5 rows

# Action 4: show — prints rows to console
df.show(3)

# Action 5: write
df.write.mode("overwrite").parquet("/data/output/")

# Action 6: foreach — applies function to each row
df.foreach(lambda row: send_to_api(row["id"]))        # Executed on workers
```

**Input:** 1 million numbers.
**Output:** Count, sample rows, written files.
**Explanation:** Each action triggers DAG execution from scratch (unless cached). `collect` brings all data to driver — only for small results. `take` and `show` are safe for sampling. `foreach` runs on workers.

## Common Mistakes
- Calling multiple actions on the same DataFrame without caching — recomputes the entire lineage each time.
- Using `collect()` on large datasets — driver OOM.
- Chaining too many transformations without understanding the execution plan.
- Not knowing that `count()` after `groupBy` requires a full shuffle.
- Assuming `take(N)` returns the first N rows by sort order (it doesn't — it's arbitrary).

## How Interviewers Ask This
- "What is lazy evaluation and why is it important?"
- "What is the difference between narrow and wide transformations?"
- "Explain how Spark pipelines transformations."
- "What happens when you call an action?"
- "How does Spark handle a shuffle?"

## How To Impress The Interviewer
- Explain that narrow transformations support pipelining — multiple operations run in one pass over data without shuffle.
- Describe the Catalyst optimization: predicate pushdown pushes filters to the data source (Parquet row groups, JDBC queries).
- Mention that wide transformations create stage boundaries. Spark writes shuffle data to disk (not memory) for fault tolerance.
- Discuss `explain("cost")` or `explain("codegen")` for deep optimization insights.
- Explain that `repartition` is always a wide transformation, `coalesce` is narrow only when reducing partitions.

## Most Asked Questions

**1. What is the difference between a transformation and an action?**
Transformations return a new DataFrame/RDD (lazy). Actions trigger computation and return a value or write to storage.

**2. Why does Spark use lazy evaluation?**
To optimize the execution plan. Spark builds a DAG and optimizes it before running — pipelining narrow transforms, pushing filters down, choosing join strategies.

**3. What is a narrow transformation?**
Each partition of the parent contributes to at most one partition of the child. Examples: `map`, `filter`, `flatMap`, `mapPartitions`, `union`. No shuffle needed.

**4. What is a wide transformation?**
Each partition of the parent feeds multiple child partitions. Requires shuffle. Examples: `groupByKey`, `reduceByKey`, `join`, `repartition`, `distinct`.

**5. What operations cause a shuffle?**
`groupBy`, `groupByKey`, `reduceByKey`, `join`, `repartition`, `coalesce` (only when increasing), `distinct`, `orderBy`, `sortBy`.

**6. What are the common actions in Spark?**
`collect()`, `count()`, `take(N)`, `show()`, `first()`, `head(N)`, `write`, `foreach`, `reduce`, `saveAsTextFile`.

**7. What happens when `collect()` is called?**
All partitions are sent to the driver. The driver deserializes and collects them into a list. Dangerous for large data.

**8. What is the difference between `count()` and `countApprox()`?**
`count()` returns exact count. `countApprox(timeoutMillis)` returns an estimate within a timeout — useful for very large datasets.

**9. What is `foreach` vs `map`?**
`foreach` is an action — applies a side-effect function (write to DB). Returns Unit. `map` is a transformation — returns a new DataFrame.

**10. How does Spark handle an action on a cached DataFrame?**
If the DataFrame is cached, Spark reads from cache instead of recomputing. If partitions are missing, Spark recomputes just those partitions via lineage.

**11. Can you call multiple actions on the same DataFrame efficiently?**
Yes, if you `cache()` or `persist()` the DataFrame. Otherwise each action recomputes the entire lineage.

**12. What is the difference between `reduce` and `fold`?**
`reduce` uses a commutative/associative function on RDD elements. `fold` has a zero value (for empty partitions). Both are actions.

**13. What is `takeSample`?**
`rdd.takeSample(withReplacement, num, seed)` — returns a random sample of exactly N elements. An action.

**14. How does Spark pipeline narrow transformations?**
Multiple narrow transformations on the same partition are combined into a single stage and executed in one pass. No intermediate data is written to disk.

**15. What is `first()` vs `take(1)`?**
`first()` returns the first element (Row). `take(1)` returns an array with one element. Both are equivalent.
