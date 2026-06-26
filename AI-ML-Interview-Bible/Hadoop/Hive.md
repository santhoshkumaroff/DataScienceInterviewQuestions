# Hive

## What is it?
Hive is a data warehouse tool built on Hadoop that provides SQL-like querying (HiveQL). It translates SQL queries into MapReduce, Tez, or Spark jobs. It's designed for batch processing of large datasets stored in HDFS, HBase, or cloud storage.

## Why do we use it?
Hive enables analysts familiar with SQL to query petabytes of data without writing MapReduce code. It supports partitioning, bucketing, and efficient file formats (ORC, Parquet) for fast queries. It's the standard for Hadoop-based data warehousing.

## Real-Time Example 1
**E-commerce Daily Sales Report:** Flipkart needs to generate daily sales reports from 50TB of data.
1. Sales data lands in HDFS partitioned by date (`/sales/dt=2025-06-26/`).
2. Analysts query: `SELECT category, SUM(amount) FROM sales WHERE dt='2025-06-26' GROUP BY category;`
3. Hive pushes down the partition filter — only scans one day's directory.
4. Query is executed on Tez, results returned in 2 minutes.
5. The report is stored in a Parquet table for further analysis.

## Real-Time Example 2
**Telecom Call Record Analysis:** Vodafone analyzes monthly call records (CDRs).
1. Raw CDRs are loaded into an external Hive table over HDFS.
2. Data is bucketed by `phone_number` into 64 buckets for efficient JOINs.
3. Queries compute average call duration, top-called numbers, revenue per customer.
4. Results are exported to MySQL for BI dashboards.

## Concept Example 1
**Creating a partitioned Hive table.**

```sql
CREATE TABLE sales (
    order_id INT,
    product STRING,
    amount DOUBLE
)
PARTITIONED BY (order_date STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;

-- Load data into a partition
LOAD DATA INPATH '/data/sales/2025-06-26.csv'
INTO TABLE sales PARTITION (order_date='2025-06-26');

-- Query a specific partition
SELECT product, SUM(amount)
FROM sales
WHERE order_date = '2025-06-26'
GROUP BY product;
```

**Input:** CSV file with order_id, product, amount columns.
**Output:** Total sales per product for a specific date.
**Explanation:** Partitioning physically separates data into directories (`order_date=2025-06-26/`). Queries with partition filters only scan relevant directories — much faster than full table scans.

## Concept Example 2
**Using ORC format and bucketing for performance.**

```sql
CREATE TABLE user_behavior (
    user_id INT,
    action STRING,
    timestamp BIGINT
)
PARTITIONED BY (dt STRING)
CLUSTERED BY (user_id) INTO 64 BUCKETS
STORED AS ORC
TBLPROPERTIES ('orc.compress'='SNAPPY');

-- Insert data with bucketing
SET hive.enforce.bucketing = true;
INSERT OVERWRITE TABLE user_behavior PARTITION (dt='2025-06-26')
SELECT id, action, ts FROM raw_logs WHERE dt='2025-06-26';
```

**Input:** Raw log data (1TB).
**Output:** Bucketed, compressed ORC table (300MB saved vs text — 70% compression).
**Explanation:** ORC is a columnar format with built-in compression and predicate pushdown. Bucketing ensures rows with the same user_id are in the same file — this makes JOINs on user_id very efficient (no shuffle).

## Common Mistakes
- Forgetting to add partitions after loading data (`MSCK REPAIR TABLE` on external tables).
- Using `GROUP BY` on unpartitioned huge tables (scans everything).
- Not using columnar formats (ORC/Parquet) — text files are up to 10x slower.
- Running Hive in MapReduce mode instead of Tez (Tez is 2-3x faster).
- Writing complex nested subqueries when CTEs or temp tables are clearer.

## How Interviewers Ask This
- "What is the difference between Hive and SQL?"
- "Explain partitioning vs bucketing."
- "How does Hive convert SQL to MapReduce/Tez?"
- "What are the advantages of ORC over Parquet?"
- "How do you handle small files in Hive?"

## How To Impress The Interviewer
- Discuss the Hive cost-based optimizer (CBO): analyze tables first for best query plans.
- Explain ACID in Hive (Hive 3.x) — INSERT, UPDATE, DELETE with transactions. Uses Delta/Base files for merge-on-read.
- Talk about LLAP (Live Long and Process) — in-memory cache for Hive that significantly speeds repeated queries.
- Mention Materialized Views in Hive 3.x — precomputed results auto-refreshed.
- Explain the difference between Internal (managed) and External tables: DROP TABLE on internal deletes data; external does not.

## Most Asked Questions

**1. What is Apache Hive?**
A data warehouse built on Hadoop for querying large datasets using SQL-like language (HiveQL). It translates queries to MapReduce/Tez/Spark jobs.

**2. What is the difference between Hive and SQL?**
HiveQL is SQL-like but limited — no full support for transactions (before Hive 3), no row-level updates/inserts. Hive is optimized for batch reads, not OLTP.

**3. What is partitioning in Hive?**
Dividing data into subdirectories based on partition keys (e.g., date, country). Queries with partition filters skip irrelevant directories, improving performance.

**4. What is bucketing in Hive?**
Dividing data into fixed number of files (buckets) based on hash of a column. Useful for efficient JOINs and sampling.

**5. What is the difference between ORC and Parquet?**
Both are columnar formats. ORC (Optimized Row Columnar) is Hive-native, has better compression with lightweight indexes. Parquet (from Cloudera/Twitter) is more widely supported by Spark, Impala, Presto. ORC is slightly better for Hive.

**6. What are Hive SerDes?**
Serializer/Deserializer — tells Hive how to read/write data in custom formats. Examples: AvroSerDe, JsonSerDe, RegexSerDe.

**7. How does Hive handle schema evolution?**
ORC supports adding columns (new columns get NULL for old data). Parquet supports schema merging. AVRO supports full schema evolution.

**8. What is HiveServer2?**
HiveServer2 allows remote clients to execute Hive queries over Thrift/JDBC/ODBC. Supports multi-session and authentication.

**9. What is the difference between Internal and External tables?**
Internal (managed) tables: Hive owns the data. DROP TABLE deletes both metadata and data. External tables: Hive only manages metadata. DROP TABLE only removes metadata; data in HDFS remains.

**10. How do you optimize Hive queries?**
- Use partitioning and bucketing.
- Store data in ORC/Parquet with Snappy compression.
- Enable CBO (`hive.cbo.enable=true`).
- Use Tez instead of MapReduce.
- Tune `hive.auto.convert.join` for map-side joins.
- Run `ANALYZE TABLE` for statistics.

**11. What is the difference between Hive and HBase?**
Hive is for batch analytics over large datasets. HBase is a NoSQL database for real-time read/write access. They solve different problems.

**12. What is a Hive View?**
A virtual table defined by a SELECT query. It does not store data — references underlying tables each time. Useful for permission control and simplifying complex queries.

**13. What is ACID support in Hive?**
Hive 3.x supports INSERT, UPDATE, DELETE with transactions. Uses Delta files (changes) + Base files (original data). Merge-on-read combines them at query time.

**14. What is LLAP in Hive?**
Live Long and Process — Hive's in-memory execution engine. It caches data in daemon processes for sub-second query responses on repeated queries.

**15. What is the metastore in Hive?**
Stores metadata (schema, partitions, table location) in a relational database (MySQL, PostgreSQL, Derby). Hive checks the metastore for schema before query execution.
