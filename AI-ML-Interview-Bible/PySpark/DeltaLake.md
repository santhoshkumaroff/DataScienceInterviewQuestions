# Delta Lake

## What is it?
Delta Lake is an open-source storage layer that brings ACID (Atomicity, Consistency, Isolation, Durability) transactions to data lakes. It runs on top of Parquet files and provides features like time travel (versioned data), schema enforcement, schema evolution, unified batch/streaming, and MERGE/UPSERT operations.

## Why do we use it?
Data lakes traditionally lack ACID guarantees — partial writes, corrupt data, inconsistency. Delta Lake solves these issues. It enables reliable data engineering (upserts instead of full overwrites), data versioning for reproducibility, and unified batch + streaming pipelines.

## Real-Time Example 1
**Financial Reconciliation:** A bank processes millions of daily transactions and needs accurate balances.
1. Raw transactions stream into a Delta table.
2. A MERGE operation updates account balances (UPSERT — update if exists, insert if not).
3. If a pipeline fails mid-write, Delta's transaction log ensures atomic rollback.
4. Auditors use time travel (`VERSION AS OF`) to verify historical balances.
5. Schema enforcement prevents bad data from corrupting the table.

## Real-Time Example 2
**E-Commerce Inventory Management:** Amazon updates inventory across 100M SKUs in real-time.
1. Supplier feeds land in a Bronze Delta table (raw).
2. Cleaning job writes to Silver Delta table (cleaned).
3. MERGE upsert updates inventory counts.
4. CDC (change data capture) from OLTP database syncs via Delta Merge.
5. Data scientists query Gold Delta table with time travel for trend analysis.

## Concept Example 1
**Delta Lake CRUD operations.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("DeltaDemo") \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog") \
    .getOrCreate()

# Create Delta table
df = spark.createDataFrame([
    (1, "Alice", 100), (2, "Bob", 200), (3, "Charlie", 150)
], ["id", "name", "amount"])
df.write.format("delta").save("/data/delta/accounts")

# UPSERT with MERGE
new_data = spark.createDataFrame([
    (2, "Bob", 250),   # Update
    (4, "Dave", 300)   # Insert
], ["id", "name", "amount"])

from delta.tables import DeltaTable
delta_table = DeltaTable.forPath(spark, "/data/delta/accounts")

delta_table.alias("target").merge(
    new_data.alias("source"),
    "target.id = source.id"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()

# Time travel
df_v1 = spark.read.format("delta") \
    .option("versionAsOf", 0) \
    .load("/data/delta/accounts")

df_v2 = spark.read.format("delta") \
    .option("timestampAsOf", "2025-06-26") \
    .load("/data/delta/accounts")
```

**Input:** Account data + CDC data.
**Output:** Table with upserted records. Time travel shows version 0 (original) vs current.
**Explanation:** Delta Lake stores a transaction log (`_delta_log/`) with each version. `MERGE` combines insert + update atomically. `versionAsOf` reads a previous version — non-destructive rollback.

## Concept Example 2
**Schema enforcement and evolution.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("SchemaEvolution") \
    .getOrCreate()

# Create table with schema
df = spark.createDataFrame([(1, "A")], ["id", "label"])
df.write.format("delta").mode("overwrite").save("/data/delta/test")

# Schema enforcement — this will FAIL
bad_df = spark.createDataFrame([(2, "B", 0.5)], ["id", "label", "score"])
# bad_df.write.format("delta").mode("append").save("/data/delta/test")
# ERROR: Incompatible schema (extra column)

# Schema evolution — allow it
bad_df.write.format("delta") \
    .mode("append") \
    .option("mergeSchema", "true") \
    .save("/data/delta/test")

# Now schema has id, label, score
spark.read.format("delta").load("/data/delta/test").printSchema()
```

**Input:** DataFrame with different schema.
**Output:** When `mergeSchema=true`, schema evolves to include new column. Without it, operation fails.
**Explanation:** By default, Delta Lake enforces schema — prevents data corruption. With `mergeSchema=true`, it allows adding new columns. This is critical for production ETL where upstream schemas evolve.

## Common Mistakes
- Not running `OPTIMIZE` and `VACUUM` — small files accumulate, storage grows unbounded.
- Confusing `VACUUM` with DELETE — VACUUM physically deletes old files (DANGER: can break time travel).
- Using Delta without Spark 3.x compatible version (version mismatch).
- Not enabling the DeltaSQL plugin in `spark.sql.extensions`.
- Running MERGE without partitioning — scans the full table for each upsert.

## How Interviewers Ask This
- "What is Delta Lake and what problem does it solve?"
- "How does Delta Lake implement ACID transactions?"
- "What is time travel and how do you use it?"
- "How does schema enforcement work in Delta Lake?"
- "Explain the MERGE operation (upsert)."

## How To Impress The Interviewer
- Explain the transaction log (`_delta_log/`): JSON files counting up (000001.json, 000002.json). Each commit is an atomic rename of `_delta_log/00000N.json` → ensures ACID.
- Discuss OPTIMIZE: Z-ordering (Zorder) clusters data by multiple columns — improves data skipping for multi-column queries.
- Explain VACUUM retention threshold (default 7 days) — must be > longest running query to avoid breaking time travel.
- Mention Delta Lake's integration with Hive metastore for schema management.
- Talk about `change data feed` — capture CDC from Delta tables for downstream consumers.

## Most Asked Questions

**1. What is Delta Lake?**
An open-source storage layer that adds ACID transactions, time travel, schema enforcement, and unified batch/streaming to data lakes on top of Parquet.

**2. What problem does Delta Lake solve?**
Traditional data lakes lack ACID — partial writes, inconsistent reads, poor data quality. Delta Lake adds transactional guarantees, versioning, and schema management.

**3. How does Delta Lake implement ACID?**
Through a transaction log (`_delta_log/`). Each write creates a new JSON log entry with a monotonically increasing version number. Atomic rename ensures consistency. Spark uses optimistic concurrency control.

**4. What is time travel in Delta Lake?**
Reading a previous version of the table. `spark.read.format("delta").option("versionAsOf", N).load(path)` or use `timestampAsOf`.

**5. What is schema enforcement?**
Delta Lake validates that data being written matches the table schema. Rejects writes with mismatched types or extra columns. Prevents data corruption.

**6. What is schema evolution?**
Allowing the table schema to change over time. Enabled with `.option("mergeSchema", "true")`. Supports adding columns, widening types.

**7. What is the MERGE operation?**
Combines INSERT, UPDATE, DELETE in a single atomic operation. Used for UPSERTS (update if exists, insert if not). Syntax: `MERGE INTO target USING source ON condition WHEN MATCHED THEN UPDATE WHEN NOT MATCHED THEN INSERT`.

**8. What is OPTIMIZE in Delta Lake?**
Compacts small files into larger ones for better read performance. Can also Z-order by columns. Command: `OPTIMIZE delta.`/path` ZORDER BY (col1, col2)`.

**9. What is VACUUM in Delta Lake?**
Physically deletes old Parquet files that are no longer referenced by the transaction log. Files must be older than `retentionThreshold` (default 7 days). Danger: removes ability to time travel.

**10. How does Delta Lake handle concurrent writes?**
Uses optimistic concurrency control. If two writes conflict, one fails and retries. Isolation levels: Serializable and Snapshot isolation.

**11. What is the difference between Delta Lake and Apache Iceberg?**
Both are table formats with ACID. Delta Lake is Databricks-backed, tighter Spark integration. Iceberg is Apache-backed, broader engine support (Flink, Trino, Spark, Hive). Iceberg has partition evolution; Delta requires table recreation.

**12. What is the Delta transaction log?**
JSON files in `_delta_log/` directory. Each file represents a committed version. Contains actions: add file, remove file, update metadata, change schema.

**13. Can Delta Lake be used with streaming?**
Yes. Delta Lake supports `readStream` and `writeStream`. It's a streaming sink and source. Exactly-once semantics with streaming.

**14. What is Delta sharing?**
An open protocol for sharing Delta tables across platforms (Databricks, Spark, Trino, Power BI). Uses a REST API to share data without copying.

**15. How do you convert Parquet to Delta?**
`CONVERT TO DELTA parquet.`/path`` — creates Delta transaction log for existing Parquet data.

**16. What is the COPY INTO command?**
Efficiently loads files (CSV, JSON, Parquet) into Delta tables with idempotent execution (skips already-loaded files). `COPY INTO delta_table FROM 's3://path/' FILEFORMAT = CSV`.

**17. What is the Lambda architecture with Delta Lake?**
Bronze (raw), Silver (cleaned), Gold (aggregated) layers. Each layer is a Delta table. This is called the Medallion Architecture. Enables incremental ETL.

**18. How does Delta Lake handle data deletion?**
`DELETE FROM delta_table WHERE condition` — removes data logically (file marked as removed in transaction log). VACUUM physically removes files later.
