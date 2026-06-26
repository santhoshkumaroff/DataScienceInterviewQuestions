# Caching

## What is it?
Caching (persistence) stores DataFrames/RDDs in memory or disk across executors so subsequent actions avoid recomputing the full lineage. Spark offers `cache()` (default MEMORY_AND_DISK) and `persist(storageLevel)` with levels like MEMORY_ONLY, MEMORY_AND_DISK, DISK_ONLY. Checkpointing saves data to reliable storage (HDFS) and truncates lineage.

## Why do we use it?
Caching is essential for iterative algorithms (ML training, graph processing), interactive queries (BI dashboards querying the same dataset), and reusing intermediate results in ETL pipelines. It reduces job runtime by 10-100x for repeated access patterns.

## Real-Time Example 1
**Machine Learning Feature Engineering:** A data science team builds features for a recommendation model.
1. Raw user interaction data (10TB) is read from HDFS.
2. Data is cleaned and transformed (sessionization, feature extraction).
3. The transformed DataFrame is cached — used to train 10 different models (ALS, XGBoost, etc.).
4. Each model training reuses the cached data — no recomputation.
5. Time saved: 8 hours per iteration → 3 hours.

## Real-Time Example 2
**Interactive BI Dashboard:** An e-commerce company runs Tableau on Spark SQL.
1. Customer 360 table (5TB) is cached in memory.
2. Multiple analysts run ad-hoc queries (revenue, churn, segmentation).
3. Queries execute in seconds instead of minutes.
4. Data is refreshed nightly — cache is invalidated and rebuilt.

## Concept Example 1
**Cache vs Persist with different storage levels.**

```python
from pyspark.sql import SparkSession
from pyspark.storagelevel import StorageLevel

spark = SparkSession.builder.appName("CachingDemo").getOrCreate()

df = spark.read.parquet("/data/sales/")

# cache() = default persist
df.cache()

# Persist with specific level
df_optimized = df.filter(df.amount > 0)
df_optimized.persist(StorageLevel.MEMORY_AND_DISK)

# Different storage levels
df1.persist(StorageLevel.MEMORY_ONLY)           # Only memory (fast, may recompute)
df2.persist(StorageLevel.MEMORY_AND_DISK_SER)   # Memory + disk, serialized
df3.persist(StorageLevel.DISK_ONLY)             # Disk only (slowest)

# First action — caches the data
df_optimized.count()

# Second action — uses cached data
result = df_optimized.groupBy("category").sum("amount").collect()

# Check what's cached
print(spark.sparkContext.getPersistentRDDs())  # Show cached RDDs

# Unpersist when done
df_optimized.unpersist()
```

**Input:** Sales DataFrame from Parquet.
**Output:** Cached DataFrame reused across actions.
**Explanation:** `cache()` is lazy — data is cached after the first action. `persist()` with different levels trades speed vs memory usage. MEMORY_ONLY is fastest, DISK_ONLY is safest. Always `unpersist()` to free resources.

## Concept Example 2
**Checkpointing for lineage truncation.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("CheckpointDemo") \
    .config("spark.sql.adaptive.enabled", "true") \
    .getOrCreate()

# Set checkpoint directory (HDFS or S3)
spark.sparkContext.setCheckpointDir("hdfs:///checkpoints/")

df = spark.range(1, 1000000)

# Long lineage simulation
for _ in range(20):
    df = df.withColumn("new", df.id + 1)

print(f"Lineage length before checkpoint: {len(df.rdd.toDebugString())}")

# Checkpoint — saves to HDFS and truncates lineage
df_checkpointed = df.checkpoint()

print(f"Lineage length after checkpoint: {len(df_checkpointed.rdd.toDebugString())}")
```

**Input:** 20 transformations chained.
**Output:** Lineage truncated after checkpoint.
**Explanation:** After `checkpoint()`, the DataFrame has no parent lineage — it reads from the checkpoint file. This prevents lineage-driven recomputation from becoming exponentially expensive in iterative algorithms.

## Common Mistakes
- Caching but never unpersisting — memory leak, other jobs suffer.
- Caching the same DataFrame multiple times with different names.
- Using `cache()` on a DataFrame that is used only once (waste).
- Expecting `cache()` to be eager — it's lazy and waits for an action.
- Caching large DataFrames with MEMORY_ONLY — data spills to disk anyway, but with MEMORY_AND_DISK it would be faster.

## How Interviewers Ask This
- "What is the difference between cache and persist?"
- "What storage levels does Spark support?"
- "When should you cache a DataFrame?"
- "What is checkpointing and when do you use it?"
- "How does caching affect fault tolerance?"

## How To Impress The Interviewer
- Explain that `cache()` is actually `persist(StorageLevel.MEMORY_AND_DISK)`.
- Describe cache behavior with lineage: if a cached partition is lost (executor failure), Spark recomputes only that partition using lineage.
- Discuss that `checkpoint()` differs from `cache()`: checkpoint truncates lineage (saves to HDFS), cache keeps lineage. Checkpoint is for iterative algorithms.
- Mention the UI: go to Storage tab in Spark UI to see cached data, sizes, and disk/memory ratios.
- Explain that serialization overhead (MEMORY_ONLY_SER) reduces memory usage but increases CPU cost.

## Most Asked Questions

**1. What is caching in Spark?**
Storing DataFrames/RDDs in cluster memory or disk to avoid recomputing the full lineage on each action.

**2. What is the difference between `cache()` and `persist()`?**
`cache()` = `persist(DEFAULT)` which is `persist(MEMORY_AND_DISK)`. `persist()` allows specifying any storage level.

**3. What are the storage levels in Spark?**
- MEMORY_ONLY: Store as deserialized Java objects in memory. May recompute if not enough memory.
- MEMORY_AND_DISK: Memory first, spill to disk.
- MEMORY_ONLY_SER: Serialized Java objects (less memory, more CPU).
- MEMORY_AND_DISK_SER: Serialized with disk spill.
- DISK_ONLY: Store only on disk.

**4. When should you cache a DataFrame?**
- Used multiple times in different actions (iterative algorithms, ML training).
- Interactive querying (same data, different queries).
- Expensive transformation chain reused by downstream consumers.

**5. When should you NOT cache?**
- Data used only once.
- Very large data that doesn't fit in memory (may cause disk spill and recomputation overhead).
- Data that changes between actions (caching could return stale results).

**6. What is checkpointing?**
Saving DataFrame/RDD to reliable storage (HDFS, S3) and truncating lineage. Safer than caching (fault-tolerant) but slower (disk I/O).

**7. What is the difference between cache and checkpoint?**
Cache keeps lineage (can recompute lost partitions). Checkpoint truncates lineage after saving to reliable storage. Checkpoint is slower but breaks long lineage chains.

**8. How does caching affect fault tolerance?**
If an executor fails, cached partitions on that executor are lost. Spark recomputes them using lineage. After checkpoint, partitions are read from checkpoint storage.

**9. How do you remove data from cache?**
`df.unpersist()` or `df.unpersist(blocking=True)` to block until removed. Or `spark.sparkContext.clearRDDInfo()`.

**10. How do you check if a DataFrame is cached?**
`spark.sparkContext.getPersistentRDDs()` lists cached RDDs. Spark UI → Storage tab shows cached tables.

**11. What is the default storage level for `cache()`?**
MEMORY_AND_DISK. This means data is stored in memory if possible, spilled to disk if needed.

**12. How does Spark decide what to evict from cache?**
LRU (Least Recently Used) eviction when memory is full. Spark automatically drops old cached partitions.

**13. Can you cache a DataFrame after transformations?**
Yes. `df.filter(...).select(...).cache()` — only the transformed result is cached. The original DataFrame is not cached.

**14. What is the impact of caching on shuffle?**
Caching before a shuffle can help if the cached data is used multiple times. During shuffle, intermediate data is written to disk anyway.

**15. What is `persist` with replication?**
`MEMORY_ONLY_2`, `MEMORY_AND_DISK_2` — replicates each partition on two nodes. Increases fault tolerance at the cost of 2x storage.
