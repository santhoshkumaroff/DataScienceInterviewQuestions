# Partitioning

## What is it?
Partitioning divides data into chunks that Spark processes in parallel. Spark supports hash partitioning (by key hash), range partitioning (by key range), and custom partitioning (via Partitioner class). Bucketing in DataFrames/SQL is a form of partitioning for optimizing joins.

## Why do we use it?
Proper partitioning ensures balanced data distribution (no stragglers), efficient joins (avoid shuffles when both sides are partitioned by the same key), and optimized parallelism. Wrong partitioning causes data skew and slow jobs.

## Real-Time Example 1
**Large-Scale User Analytics:** A social media company partitions user event data.
1. Raw events: 200TB/day across 1000 files.
2. Repartitioned by `user_id % 512` (hash) — 512 partitions.
3. Queries by user_id scan only one partition.
4. All events for a user co-located in the same partition.
5. Join with user profile table on `user_id` is a map-side join.

## Real-Time Example 2
**Time-Series Sensor Data:** An IoT company processes millions of sensor readings.
1. Sensors stream data partitioned by `device_id` and `hour`.
2. Range partitioning by `timestamp` enables efficient time-range queries.
3. Partitions are balanced — each executors processes similar data volumes.
4. Old partitions are dropped (retention) by deleting directories.

## Concept Example 1
**Hash partitioning vs Range partitioning.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Partitioning").getOrCreate()

df = spark.read.parquet("/data/sensors/")

# Hash partitioning
df_hash = df.repartition(10, "device_id")
df_hash.explain()
# Output shows: Exchange hashpartitioning(device_id, 10)

# Range partitioning
from pyspark.sql.functions import col
df_range = df.repartitionByRange(10, col("device_id"))
df_range.explain()
# Output shows: Exchange rangepartitioning(device_id, 10)

# Check partition distribution
print(df_hash.rdd.glom().map(len).collect())
print(df_range.rdd.glom().map(len).collect())
```

**Input:** Sensor data with device_id column.
**Output:** Two DataFrames partitioned differently.
**Explanation:** Hash partitioning uses `hash(device_id) % 10` — efficient but can be skewed. Range partitioning assigns contiguous ranges of `device_id` to each partition — better for sorting and range queries.

## Concept Example 2
**Custom partitioning for skewed data.**

```python
from pyspark import SparkContext
from pyspark.rdd import portable_hash

# Detect skewed keys (RDD approach)
skewed_rdd = df.rdd.map(lambda row: (row["device_id"], row))
key_counts = skewed_rdd.countByKey()
skewed_keys = {k for k, v in key_counts.items() if v > 10000}

# Custom partitioner to handle skew
class CustomPartitioner:
    def __init__(self, num_partitions, skewed_keys):
        self.num_partitions = num_partitions
        self.skewed_keys = skewed_keys

    def __call__(self, key):
        if key in self.skewed_keys:
            # Spread skewed key across multiple partitions
            return portable_hash(key + "_suffix") % self.num_partitions
        return portable_hash(key) % self.num_partitions

# Apply custom partitioning
balanced = skewed_rdd.partitionBy(50, CustomPartitioner(50, skewed_keys))
```

**Input:** Skewed device_id distribution (some devices have 100x more data).
**Output:** Balanced partitions — no single partition gets overloaded.
**Explanation:** Default hash partitioning puts all data for one key in one partition. Custom partitioning spreads large keys across multiple partitions for balanced workload.

## Common Mistakes
- Using `repartition()` with too many partitions (task scheduling overhead).
- Not repartitioning before joins — causes full shuffle for both sides.
- Forgetting that `partitionBy` on write creates directories (can cause small files).
- Assuming `repartitionByRange` always ensures equal-sized partitions.
- Not considering data growth when choosing partition count.

## How Interviewers Ask This
- "What is partitioning in Spark and why is it important?"
- "What is the difference between hash and range partitioning?"
- "How do you create a custom partitioner?"
- "How does partitioning affect join performance?"
- "What is bucketing and how is it different from partitioning?"

## How To Impress The Interviewer
- Explain that partitioning at write time (`df.write.partitionBy("date")`) creates directory structure — useful for partition pruning at read time.
- Discuss bucketing: `CLUSTERED BY (key) INTO N BUCKETS` — when both tables are bucketed by the same key with same bucket count, Spark performs bucketed join (no shuffle).
- Mention that `repartition()` causes a full shuffle while `coalesce()` minimizes data movement.
- Explain that for skewed data, use salting or custom partitioner — Spark 3.x AQE can handle skew automatically.
- Talk about partition pruning in Hive/Spark SQL — reading only matching directories.

## Most Asked Questions

**1. What is partitioning in Spark?**
Dividing data into smaller chunks (partitions) that can be processed in parallel across executors. Each partition is a logical chunk of data.

**2. What is the difference between hash partitioning and range partitioning?**
Hash: `hash(key) % N` — fast, data is evenly distributed (if hash is uniform). Range: data is sorted and split into ranges — better for ordered processing and range queries.

**3. How do you repartition a DataFrame?**
`df.repartition(N, "column")` — hash partition by column. `df.repartitionByRange(N, col("column"))` — range partition.

**4. What is the default partitioning in Spark?**
For `parallelize`: number of slices (default 2 or `spark.default.parallelism`). For `textFile`: number of HDFS blocks (splits). After shuffle: `spark.sql.shuffle.partitions` (default 200).

**5. What is bucketing in Spark SQL?**
Writing data into a fixed number of files based on hash of a column. `CLUSTERED BY (id) INTO 64 BUCKETS`. Optimizes joins and aggregations on that column.

**6. How does bucketing improve join performance?**
If both tables are bucketed by the join key with the same bucket count, Spark joins bucket-to-bucket — no shuffle needed.

**7. What is a custom partitioner?**
A user-defined function that determines which partition a key goes to. Useful for handling data skew or domain-specific distribution.

**8. How do you check partition distribution?**
`df.rdd.glom().map(len).collect()` — returns the size of each partition. `df.rdd.getNumPartitions()` — returns partition count.

**9. What is the difference between `partitionBy` on write vs `repartition`?**
`df.write.partitionBy("date")` creates directory structure (Hive-style) for data skipping. `df.repartition("date")` redistributes data in memory for processing.

**10. How do you handle data skew in partitions?**
- Salting: add random suffix to skewed keys.
- Custom partitioner: spread large keys across partitions.
- AQE skew join (Spark 3.x): handles skewed join partitions automatically.

**11. What is partition pruning?**
Spark SQL automatically skips partitions that don't match query filters. For partitioned tables, `WHERE date='2025-01-01'` only reads that directory.

**12. How many partitions should you have?**
Rule of thumb: 2-4x the number of executor cores. Not too few (underutilized cluster, OOM on large partitions). Not too many (task scheduling overhead).

**13. What happens if partitions are too large?**
Tasks may spill to disk (slow), cause OOM, or fail. A single partition should fit in executor memory.

**14. What happens if partitions are too many?**
High task scheduling overhead, small output files, increased shuffle overhead.

**15. What is the default partition count when reading from HDFS?**
One partition per HDFS block (default 128MB split). For 1TB file with 128MB blocks → ~8000 partitions.

**16. What is the difference between `coalesce` and `repartition` in terms of partitioning?**
`coalesce` reduces partition count by merging adjacent partitions. `repartition` can increase or decrease count by shuffling all data.

**17. How do you avoid small files when writing partitioned data?**
Use `coalesce(N)` before write. Set `spark.sql.files.maxRecordsPerFile`. Use Delta `OPTIMIZE` to compact files.

**18. What is dynamic partition insert?**
Writing to a partitioned table where partition values are determined from the data. Use `INSERT OVERWRITE TABLE t PARTITION (date) SELECT ... FROM ...`.
