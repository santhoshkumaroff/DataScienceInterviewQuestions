# PySpark Interview Questions (40+ Questions)

## Spark Architecture & Fundamentals

**1. What is Apache Spark?**
Spark is a unified, distributed data processing engine for large-scale data. It runs on YARN, Mesos, Kubernetes, or standalone. Supports batch, streaming, SQL, ML, and graph processing.

**2. Explain Spark architecture (Driver, Executors, Cluster Manager).**
- **Driver:** Runs the main program, creates SparkContext, builds DAG, schedules tasks.
- **Executors:** Worker processes on nodes — run tasks, store cached data.
- **Cluster Manager:** YARN, Mesos, K8s, Standalone — allocates resources.

**3. What is a DAG in Spark?**
A Directed Acyclic Graph of computation stages. Spark's DAG Scheduler converts transformations into stages (pipeline narrow transforms, split at wide transforms/shuffles).

**4. What is the difference between Spark and Hadoop MapReduce?**
Spark processes data in-memory (up to 100x faster). MapReduce writes intermediate data to disk. Spark has a unified API (SQL, ML, Streaming), MapReduce only has map/reduce.

**5. What is lazy evaluation?**
Transformations are not executed immediately. Spark builds a DAG and optimizes it. Computation happens only when an action (count, collect, save) is called.

**6. Explain client vs cluster deploy mode.**
- **Client:** Driver runs on the machine submitting the app — for interactive/development.
- **Cluster:** Driver runs inside a YARN container or K8s pod — for production.

**7. What is the SparkSession?**
The unified entry point in Spark 2.0+. Replaces SparkContext, SQLContext, HiveContext, StreamingContext. `SparkSession.builder.appName().getOrCreate()`.

**8. How does Spark manage memory?**
Reserved (300MB) + User Memory + Execution Memory (shuffle, joins) + Storage Memory (cache). Configurable via `spark.memory.fraction` and `spark.memory.storageFraction`.

## RDD

**9. What is an RDD?**
Resilient Distributed Dataset — an immutable, partitioned collection of objects that can be processed in parallel. Automatically recovers from failures via lineage.

**10. What is the difference between transformation and action?**
Transformations (map, filter) return a new RDD — lazy. Actions (count, collect) trigger computation.

**11. How does RDD achieve fault tolerance?**
Through lineage — the DAG of parent RDDs and transformations. If a partition is lost, Spark recomputes it from the source.

**12. What is the difference between narrow and wide transformations?**
Narrow: one parent partition → one child partition (no shuffle, e.g., map, filter). Wide: one parent partition → multiple child partitions (requires shuffle, e.g., reduceByKey, join).

**13. What is the difference between reduceByKey and groupByKey?**
`reduceByKey` performs map-side combine (much faster). `groupByKey` shuffles all values as iterables (slower, memory-intensive).

**14. How do you create an RDD?**
`sc.parallelize(list)`, `sc.textFile("hdfs://...")`, `sc.wholeTextFiles("dir")`, from existing RDD via transformations.

**15. What is RDD persistence?**
Storing RDD in memory/disk to avoid recomputation. Levels: MEMORY_ONLY, MEMORY_AND_DISK, DISK_ONLY, etc.

## DataFrames

**16. What is a DataFrame?**
A distributed collection of data with named columns (like a SQL table). Higher-level API than RDD, optimized via Catalyst.

**17. How is DataFrame different from RDD?**
DataFrame has schema + Catalyst optimizer + Tungsten execution. RDD is low-level, no optimization. DataFrames are 2-5x faster.

**18. How do you create a DataFrame?**
`spark.read.csv/json/parquet(path)`, `spark.createDataFrame(data, schema)`, `spark.sql("SELECT ...")`, `rdd.toDF()`.

**19. What is the Catalyst optimizer?**
Spark's query optimizer. Phases: Analysis → Logical Optimization → Physical Planning → Code Generation (Tungsten).

**20. How do you convert between RDD and DataFrame?**
RDD to DataFrame: `rdd.toDF(["col1", "col2"])`. DataFrame to RDD: `df.rdd`.

**21. What is Tungsten?**
Spark's execution engine for efficient bytecode generation, off-heap memory, and cache-aware processing.

## Transformations & Actions

**22. What is the difference between map and flatMap?**
`map`: One input → one output. `flatMap`: One input → zero or more outputs (flattens result).

**23. What operations trigger a shuffle?**
`groupBy`, `join`, `reduceByKey`, `repartition`, `distinct`, `orderBy`, `sortBy`, `intersect`.

**24. What is the difference between repartition and coalesce?**
`repartition` is a full shuffle (can increase/decrease partitions). `coalesce` merges partitions (can only decrease, no full shuffle). Coalesce is cheaper.

**25. What is the difference between `collect()` and `take()`?**
`collect()` retrieves all data to the driver (dangerous for large data). `take(N)` retrieves the first N rows.

## Joins

**26. What is a broadcast join?**
Spark sends the smaller DataFrame to all executors — each performs join locally. No shuffle. Requires one table to fit in executor memory.

**27. When does Spark use Sort-Merge join?**
When both DataFrames are too large for broadcast. Spark sorts both sides by key, then merges.

**28. How do you handle skew in joins?**
Salt the skewed key (add random suffix), replicate the small table, join in two phases. Or use AQE skew join (Spark 3+).

**29. What join types does Spark support?**
Inner, Left Outer, Right Outer, Full Outer, Left Semi, Left Anti, Cross.

**30. What is the broadcast join threshold?**
`spark.sql.autoBroadcastJoinThreshold` — default 10MB. Tables smaller than this are auto-broadcast.

## Optimization

**31. How do you set shuffle partitions?**
`spark.conf.set("spark.sql.shuffle.partitions", N)`. Default: 200. Rule: 2-4x executor cores.

**32. What is AQE (Adaptive Query Execution)?**
Spark 3.x feature that re-optimizes queries at runtime: coalesces partitions, switches join strategies, handles skew.

**33. How do you optimize reading Parquet files?**
- Schema pruning (read only needed columns).
- Predicate pushdown (filter close to source).
- Disable `mergeSchema` if not needed.
- Use Snappy/ZSTD compression.

**34. What is predicate pushdown?**
Spark pushes filter conditions to data source. Parquet row groups skip non-matching data. JDBC adds WHERE clauses.

**35. What are the key executor configurations?**
`spark.executor.instances`, `spark.executor.cores`, `spark.executor.memory`. Leave 1 core and 1GB for OS per node.

## Partitioning

**36. What is partitioning in Spark?**
Dividing data into chunks for parallel processing. Controlled by `repartition()`, `coalesce()`, or Hive-style partition columns.

**37. What is bucketing?**
Writing data into a fixed number of files based on hash of a column. Enables bucket-to-bucket joins (no shuffle).

**38. What is the default partition count when reading HDFS?**
One partition per HDFS block (128MB split). For 1TB → ~8000 partitions.

**39. How do you check partition distribution?**
`df.rdd.glom().map(len).collect()` — shows row count per partition.

## Caching & Checkpointing

**40. What is the difference between cache and persist?**
`cache()` = `persist(StorageLevel.MEMORY_AND_DISK)`. `persist()` allows specifying any level.

**41. When should you cache a DataFrame?**
- Used in multiple actions.
- Expensive to recompute.
- Iterative algorithms (ML training).

**42. What is checkpointing?**
Saving data to reliable storage (HDFS/S3) and truncating lineage. Essential for long-running iterative jobs.

## Delta Lake

**43. What is Delta Lake and why use it?**
ACID transactions on data lakes, time travel, schema enforcement, MERGE/UPSERT. Solves data quality and reliability issues in data lakes.

**44. What is time travel in Delta Lake?**
Reading previous versions of data: `.option("versionAsOf", N)` or `.option("timestampAsOf", "...")`.

**45. What is MERGE in Delta Lake?**
Atomic UPSERT: INSERT new rows + UPDATE existing rows. `MERGE INTO target USING source ON key WHEN MATCHED THEN UPDATE WHEN NOT MATCHED THEN INSERT`.

**46. What is OPTIMIZE?**
Compacts small files and Z-orders data for better read performance. `OPTIMIZE delta./path ZORDER BY (col)`.

**47. What is the difference between DELETE and VACUUM?**
DELETE marks files as removed in transaction log (logical). VACUUM physically removes old files (can break time travel if within retention period).

## General

**48. What is the difference between Spark SQL and Hive?**
Spark SQL uses Catalyst optimizer + Tungsten; faster. Hive uses MapReduce/Tez. Spark SQL supports SQL 2016. Both support Hive metastore.

**49. How do you debug a slow PySpark job?**
Check Spark UI (Stages, SQL, Storage tabs). Look for: data skew, large shuffle, spilled data, GC overhead, straggler tasks. Use `df.explain()`.

**50. How does Spark ensure fault tolerance?**
Lineage for RDDs (recompute on failure). Executor failure → tasks re-run. Driver failure → must restart (unless using checkpoint/ZooKeeper for HA).

**51. What is the difference between Spark Streaming and Structured Streaming?**
Spark Streaming (DStreams) uses micro-batches of RDDs. Structured Streaming (DataFrames) uses continuous processing, event-time windows, exactly-once semantics. Structured Streaming is newer and preferred.

**52. What is the role of the broadcast variable?**
Read-only variable cached on all executors. Useful for lookups (country codes, ML models). `spark.sparkContext.broadcast(lookupMap)`.

**53. What is an accumulator?**
A write-only variable that executors can add to (counts, sums). Driver reads the final value. `spark.sparkContext.accumulator(0)`.

**54. How do you handle large shuffles?**
- Increase shuffle partitions.
- Enable AQE (auto coalesce).
- Use broadcast joins where possible.
- Reduce data before shuffle (filter + aggregate earlier).

**55. What is the difference between union and unionByName?**
`union`: Combines by position. `unionByName`: Combines by column name (handles different column orders).

**56. What is the window function in PySpark?**
Over-specifies which rows to aggregate. `Window.partitionBy("col").orderBy("col2")`. Used with `row_number()`, `rank()`, `lag()`, `lead()`, `sum()`.

**57. How do you handle duplicates?**
`df.dropDuplicates()` or `df.drop_duplicates(["key_cols"])`. For window-based dedup: `row_number()` over partition by key order by timestamp.

**58. How do you read multiple files at once?**
`spark.read.csv(["file1.csv", "file2.csv"])`. Or read a directory: `spark.read.csv("/data/logs/")`.

**59. What is the difference between append and overwrite write modes?**
`append`: Adds new data to existing. `overwrite`: Replaces existing data entirely. Others: `error` (default — fail if exists), `ignore` (silently skip).

**60. How do you implement incremental ETL with PySpark?**
Using Delta Lake + Structured Streaming. Process only new data by tracking max timestamp or using Delta change data feed.

**61. What is the significance of `spark.sql.shuffle.partitions`?**
Controls the number of partitions after shuffle operations (joins, aggregations). Default 200. Must be tuned per workload.

**62. How does Spark achieve exactly-once semantics in streaming?**
Idempotent sinks (HDFS, Delta Lake) + checkpointing (offset tracking). Structured Streaming uses WAL for fault-tolerant state management.
