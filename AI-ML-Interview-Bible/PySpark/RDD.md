# RDD (Resilient Distributed Dataset)

## What is it?
RDD is the fundamental data structure in Spark. It is an immutable, distributed collection of objects that can be processed in parallel. RDDs automatically recover from failures using lineage. They support two types of operations: transformations (lazy) and actions (eager).

## Why do we use it?
RDDs provide fault tolerance without replication — lineage tracks how to recompute partitions. They give low-level control over data partitioning and persistence. Though DataFrames are preferred today, RDDs are essential for custom operations and understanding Spark internals.

## Real-Time Example 1
**Genomic Data Processing:** A bio-informatics company processes DNA sequence data.
1. Raw sequence files (FASTA format) are loaded as RDD of strings.
2. RDD transformations filter invalid sequences, split into k-mers, and count frequencies.
3. Custom partitioning distributes sequences by chromosome for balanced processing.
4. Lineage allows replaying computations if a node fails.
5. Results are saved back to HDFS.

## Real-Time Example 2
**Log Parsing for Security Monitoring:** A SOC analyzes server logs for threats.
1. Raw logs are loaded into an RDD from HDFS.
2. Filter transformation removes known-good IPs.
3. Map transformation extracts (IP, timestamp, event_type).
4. ReduceByKey counts events per IP.
5. Action `take()` returns the top suspicious IPs to the security team.

## Concept Example 1
**Creating RDDs and basic transformations.**

```python
from pyspark import SparkContext

sc = SparkContext("local", "RDDExample")

# Create RDD from a list
data = [1, 2, 3, 4, 5, 6]
rdd = sc.parallelize(data, numSlices=3)

# Transformations (lazy)
squared = rdd.map(lambda x: x * x)
even = squared.filter(lambda x: x % 2 == 0)

# Action (eager)
result = even.collect()
print(result)  # [4, 16, 36]

# Check number of partitions
print(rdd.getNumPartitions())  # 3
```

**Input:** List of integers `[1, 2, 3, 4, 5, 6]`.
**Output:** `[4, 16, 36]` — squares of even numbers.
**Explanation:** `parallelize` creates an RDD with 3 partitions. `map` and `filter` are lazy — they build lineage. `collect` triggers actual computation and bring results to driver.

## Concept Example 2
**RDD lineage and fault tolerance.**

```python
from pyspark import SparkContext

sc = SparkContext("local", "LineageExample")

rdd = sc.textFile("hdfs:///logs/weblogs.txt")
# Track lineage
print(rdd.toDebugString())

# Transformation chain
ips = rdd.map(lambda line: line.split()[0])  # Extract IP
errors = ips.filter(lambda ip: "500" in ip)
counts = errors.map(lambda ip: (ip, 1))
result = counts.reduceByKey(lambda a, b: a + b)

# Debug lineage
print(result.toDebugString())

# Action
output = result.collect()
```

**Input:** Multi-line web server log file.
**Output:** Per-IP count of 500 errors.
**Explanation:** Each `map`, `filter`, `reduceByKey` adds to the DAG lineage. `toDebugString` shows the full dependency graph. If a partition is lost, Spark traces lineage to recompute only that partition.

## Common Mistakes
- Using `collect()` on large RDDs — crashes the driver.
- Confusing transformations (lazy) with actions — forgetting an action means no computation.
- Not specifying enough partitions — leads to uneven data distribution.
- Creating many objects inside `map()` functions (garbage collection overhead).
- Using RDD where DataFrame would be faster (DataFrames use Catalyst + Tungsten).

## How Interviewers Ask This
- "What is an RDD and why is it resilient?"
- "Explain lineage in Spark RDDs."
- "What is the difference between transformation and action?"
- "How does RDD fault tolerance work?"
- "When would you use RDD over DataFrame?"

## How To Impress The Interviewer
- Explain that RDD resiliency comes from lineage (no replication needed). Each RDD knows its parent and how to recompute itself.
- Discuss narrow vs wide dependencies: narrow (map, filter) — no shuffle, recompute one partition. Wide (reduceByKey, join) — requires shuffle, recompute all ancestors.
- Mention `checkpoint` for cutting long lineages — useful for iterative ML algorithms.
- Talk about the `persist()` levels: MEMORY_ONLY, MEMORY_AND_DISK, DISK_ONLY — trade-off between speed and reliability.

## Most Asked Questions

**1. What is an RDD?**
RDD (Resilient Distributed Dataset) is Spark's fundamental data structure. It's an immutable, partitioned collection of objects that can be processed in parallel.

**2. Why is RDD called resilient?**
Because of lineage — if a partition is lost, Spark can recompute it using the DAG of transformations that created it.

**3. What is the difference between transformation and action?**
Transformations (map, filter) return a new RDD — lazily evaluated. Actions (count, collect, save) trigger actual computation and return a result to the driver or write to storage.

**4. How do you create an RDD?**
- From a collection: `sc.parallelize([1,2,3])`
- From external storage: `sc.textFile("hdfs://...")`, `sc.sequenceFile(...)`
- From an existing RDD: `rdd.map(...)`, `rdd.filter(...)`

**5. What is RDD lineage?**
A directed graph of parent RDDs and transformations that produced the current RDD. Spark uses lineage to recompute lost partitions.

**6. How does an RDD handle fault tolerance?**
Through lineage. If a partition is lost, Spark re-executes the transformation chain on the source data to rebuild that partition.

**7. What are the types of RDD dependencies?**
- **Narrow:** Each parent partition feeds at most one child partition (map, filter). No shuffle needed.
- **Wide:** Each parent partition feeds multiple child partitions (reduceByKey, groupBy). Requires shuffle.

**8. What is the difference between RDD and DataFrame?**
RDD: Low-level, type-safe (Java/Scala), no schema, no optimization. DataFrame: High-level, SQL-like, uses Catalyst optimizer + Tungsten, automatically optimized.

**9. How do you specify the number of partitions for an RDD?**
`sc.parallelize(data, numSlices=N)` or `rdd.repartition(N)` or `rdd.coalesce(N)`.

**10. What is the difference between repartition and coalesce?**
`repartition(N)`: Shuffles all data into N partitions (full shuffle). `coalesce(N)`: Merges existing partitions without full shuffle (only reduces partitions). Coalesce is cheaper.

**11. What is RDD persistence and what are the storage levels?**
Persistence stores RDD data in memory/disk for reuse. Levels: MEMORY_ONLY, MEMORY_AND_DISK, DISK_ONLY, MEMORY_ONLY_SER, MEMORY_AND_DISK_SER.

**12. What does `reduceByKey` do?**
Combines values with the same key using a function. It performs map-side combine for efficiency. `val rdd2 = rdd.reduceByKey(_ + _)`.

**13. What is the difference between `reduceByKey` and `groupByKey`?**
`reduceByKey` combines values before shuffle (map-side combine) — much faster. `groupByKey` shuffles all values as iterables — slower and memory-intensive.

**14. What is a Partitioner in RDD?**
Controls how keys are distributed across partitions. Default: HashPartitioner. Can use RangePartitioner or custom Partitioner.

**15. What happens when you cache an RDD?**
Spark stores the RDD's partitions in memory. Subsequent actions use the cached version instead of recomputing from lineage. Cache is lazy — triggered by the first action.
