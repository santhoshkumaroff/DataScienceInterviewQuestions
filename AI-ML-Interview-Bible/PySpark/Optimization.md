# Optimization

## What is it?
Spark optimization involves techniques to improve query performance: Catalyst optimizer (logical and physical query optimization), Tungsten (efficient code generation and memory management), partition tuning (coalesce vs repartition, shuffle partitions), data serialization (Kryo), and caching strategies.

## Why do we use it?
An unoptimized Spark job can take hours and waste cluster resources. Optimization can reduce runtime by 10-100x by minimizing shuffles, reducing data scans, and eliminating spurious recomputation. Understanding optimization is crucial for production-grade Spark.

## Real-Time Example 1
**E-commerce Batch Processing:** A retailer processes 100TB of sales data nightly.
1. Before optimization: Job takes 6 hours (huge shuffle, 200 default partitions, no predicate pushdown).
2. After optimization: Tuned shuffle partitions (2000), broadcast joins, predicate pushdown, ORC with ZSTD.
3. Runtime: 45 minutes. Resource savings: 60% reduction in compute costs.

## Real-Time Example 2
**AdTech Click Attribution:** A startup processes clickstream data for attribution modeling.
1. 500GB compressed Parquet daily. Used default settings.
2. Optimized: coalesce saved files (2000→200 partitions before write), Kryo serialization for UDF data.
3. Used `optimize_write` and dynamic partition pruning.
4. I/O reduced by 70%, job completion time halved.

## Concept Example 1
**Coalesce vs Repartition.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("CoalesceRepartition").getOrCreate()

df = spark.read.parquet("/data/large/").repartition(200)

# Coalesce (reduce partitions — minimizes shuffle)
df_coalesced = df.coalesce(10)
print(f"Coalesced partitions: {df_coalesced.rdd.getNumPartitions()}")  # 10

# Repartition (increase OR decrease — full shuffle)
df_repartitioned = df.repartition(50)
print(f"Repartitioned partitions: {df_repartitioned.rdd.getNumPartitions()}")  # 50

# Repartition by column (for join optimization)
df_by_date = df.repartition(100, "date")
```

**Input:** DataFrame with 200 partitions.
**Output:** Coalesce reduces to 10 (no shuffle). Repartition reduces to 50 (full shuffle).
**Explanation:** `coalesce` combines existing partitions without full data redistribution — cheaper. `repartition` always shuffles and creates new partitions. Use `coalesce` to reduce partitions (e.g., before writing small output files). Use `repartition` for even data distribution.

## Concept Example 2
**Shuffle partition tuning.**

```python
from pyspark.sql import SparkSession

# Set shuffle partitions before joins/aggregations
spark = SparkSession.builder \
    .appName("ShuffleTuning") \
    .config("spark.sql.shuffle.partitions", "200") \
    .getOrCreate()

# Recommendation: 2-4x number of cores

# For large datasets, use adaptive query execution (Spark 3+)
spark.conf.set("spark.sql.adaptive.enabled", "true")
spark.conf.set("spark.sql.adaptive.coalescePartitions.enabled", "true")

# Example: join with tuned shuffle
df1 = spark.read.parquet("/data/large1/")
df2 = spark.read.parquet("/data/large2/")
result = df1.join(df2, "key").groupBy("category").count()
result.explain()  # Check plan for shuffle partitions
```

**Input:** Two large DataFrames.
**Output:** Joined and aggregated.
**Explanation:** `spark.sql.shuffle.partitions` controls the number of partitions after a shuffle. Default is 200. If data is small, 200 creates many small files. If large, 200 creates huge partitions (disk spill). AQE (Adaptive Query Execution) can dynamically adjust this.

## Common Mistakes
- Using the default 200 shuffle partitions for all jobs (too few for large data, too many for small data).
- Not using partitioning columns for large tables (full scans on every query).
- Repartitioning to a huge number of partitions (increases shuffle and task scheduling overhead).
- Not enabling AQE and dynamic partition pruning (Spark 3.x features).
- Collecting large results to the driver for debugging.

## How Interviewers Ask This
- "How does Catalyst optimizer work?"
- "What is Tungsten in Spark?"
- "How do you tune shuffle partitions?"
- "What is the difference between coalesce and repartition?"
- "How do you optimize a slow PySpark job?"

## How To Impress The Interviewer
- Explain Catalyst phases: Parsing → Analysis → Logical Optimization → Physical Planning → Code Generation (Tungsten).
- Tungsten: Off-heap memory (sun.misc.Unsafe), cache-aware layout, whole-stage code generation (JVM bytecode generation).
- Explain that `spark.sql.adaptive.enabled=true` in Spark 3+ coalesces shuffle partitions and converts sort-merge to broadcast joins at runtime.
- Discuss predicate pushdown: Spark pushes filters to data source (Parquet row group skipping, JDBC WHERE clause).
- Talk about serialization: Kryo is 10x faster than Java serialization. Register custom classes for best perf.

## Most Asked Questions

**1. What is the Catalyst optimizer?**
Spark's query optimizer that transforms SQL and DataFrame operations into an optimized execution plan. It has four phases: Analysis → Logical Optimization → Physical Planning → Code Generation.

**2. What is Tungsten?**
Spark's execution engine that generates optimized bytecode, manages memory off-heap, and uses cache-aware data structures. Makes Spark 2-5x faster.

**3. What is the difference between coalesce and repartition?**
`coalesce`: Reduces partitions without full shuffle (merges existing partitions). `repartition`: Changes partition count with full shuffle. Use `coalesce` for reducing, `repartition` for increasing.

**4. How do you set shuffle partitions for a specific job?**
`spark.conf.set("spark.sql.shuffle.partitions", N)`. Rule of thumb: 2-4x the number of executor cores. Default: 200.

**5. What is Adaptive Query Execution (AQE)?**
Spark 3.x feature that re-optimizes the query plan at runtime using statistics from completed stages. Can coalesce partitions, change join strategies, and handle skew.

**6. How do you optimize a slow join?**
- Broadcast small tables.
- Repartition both tables by join key before joining (bucket join).
- Use AQE for dynamic optimization.
- Filter rows before joining.

**7. What is predicate pushdown?**
Spark pushes filter conditions to the data source. Parquet skips row groups that don't match. JDBC adds WHERE clause to SQL queries. Reduces data read from disk.

**8. How do you avoid small file problems?**
Use `coalesce(N)` or `repartition(N)` before writing. Configure `spark.sql.files.maxRecordsPerFile`. Use Delta Lake `OPTIMIZE`.

**9. What is dynamic partition pruning?**
Spark uses a filter from one side of a join to prune partitions on the other side. For example, joining a large partitioned table with a small table — Spark reads only matching partitions.

**10. How does Kryo serialization improve performance?**
Kryo is faster (10x) and more compact than Java serialization. Enable: `spark.serializer = org.apache.spark.serializer.KryoSerializer`. Register classes with `spark.kryo.registrator` for best performance.

**11. What is the impact of `spark.executor.memory`?**
Controls memory per executor. Too low → disk spill (slow). Too high → GC overhead, fewer executors per node. Typically 4-8GB per executor.

**12. What are the key executor configuration parameters?**
`spark.executor.instances` (number), `spark.executor.cores` (CPU), `spark.executor.memory` (RAM). Formula: Leave 1 core and 1GB for OS/YARN per node.

**13. What is data skew and how do you handle it?**
Data skew: Some partitions have more data than others. Fix: Salting (add random prefix to join keys), repartition by skewed column, or use AQE skew join optimization.

**14. What is the difference between `cache()` and `checkpoint()`?**
`cache()`: Keeps in memory/disk, lineage preserved. `checkpoint()`: Saves to reliable storage (HDFS), truncates lineage. Use checkpoint for iterative algorithms with long lineage.

**15. How do you optimize reading from Parquet?**
- Use schema pruning (read only required columns).
- Enable predicate pushdown.
- Set `spark.sql.parquet.mergeSchema=false`.
- Use ZSTD or Snappy compression.

**16. What is whole-stage code generation?**
Tungsten generates JVM bytecode that combines multiple operations into a single function. Avoids virtual function calls and intermediate data materialization.

**17. How do you tune garbage collection for Spark?**
Use G1GC (`-XX:+UseG1GC`), set `spark.executor.extraJavaOptions`. Monitor GC stats in Spark UI. Unified memory model reduces GC overhead.
