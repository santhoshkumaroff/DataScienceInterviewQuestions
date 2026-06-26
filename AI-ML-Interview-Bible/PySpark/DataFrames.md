# DataFrames

## What is it?
A DataFrame is a distributed collection of data organized into named columns (like a SQL table). It provides a higher-level API than RDDs with built-in query optimizations through the Catalyst optimizer and Tungsten execution engine. DataFrames can be created from structured files, SQL tables, or existing RDDs.

## Why do we use it?
DataFrames are the primary Spark API for structured data. They automatically optimize queries via Catalyst, generate efficient bytecode via Tungsten, and support SQL queries seamlessly. They simplify code — you write declarative queries and Spark handles optimization.

## Real-Time Example 1
**Customer 360 View:** A telecom company builds a unified customer view.
1. Customer data arrives from PostgreSQL (name, plan, tenure).
2. Usage data comes from Kafka streams (calls, data, SMS).
3. Billing data is loaded from Parquet files.
4. PySpark DataFrames join all datasets using SQL-like syntax.
5. The result (2TB customer 360 table) is saved back as optimized Parquet.

## Real-Time Example 2
**Financial Risk Reporting:** An investment bank generates daily risk reports.
1. Trade data (millions of transactions) is loaded as a DataFrame.
2. Aggregations compute VaR, exposure, and P&L per portfolio.
3. Window functions rank portfolios by risk.
4. The optimized DataFrame plan is 5x faster than equivalent RDD code.
5. Results are exported to Excel for compliance teams.

## Concept Example 1
**Creating and querying a DataFrame.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("DataFrameDemo").getOrCreate()

# Create DataFrame from a list of tuples
data = [("Alice", 34, "Sales"), ("Bob", 28, "IT"), ("Charlie", 45, "HR")]
columns = ["Name", "Age", "Dept"]
df = spark.createDataFrame(data, columns)

# Show data
df.show()
# +-------+---+-----+
# |   Name|Age| Dept|
# +-------+---+-----+
# |  Alice| 34|Sales|
# |    Bob| 28|   IT|
# |Charlie| 45|   HR|
# +-------+---+-----+

# DataFrame API
df.filter(df.Age > 30).select("Name", "Dept").show()

# SQL query
df.createOrReplaceTempView("employees")
spark.sql("SELECT Name, Dept FROM employees WHERE Age > 30").show()
```

**Input:** Tuple data with Name, Age, Dept.
**Output:** Filtered rows where Age > 30.
**Explanation:** `createDataFrame` infers schema from data. Both DataFrame API and SQL yield the same result — Catalyst optimizes both paths identically.

## Concept Example 2
**Reading from Parquet with schema.**

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType, DoubleType

spark = SparkSession.builder.appName("ParquetRead").getOrCreate()

# Define schema (better performance than inference)
schema = StructType([
    StructField("user_id", IntegerType(), True),
    StructField("name", StringType(), True),
    StructField("purchase_amount", DoubleType(), True)
])

# Read Parquet with schema
df = spark.read.schema(schema).parquet("/data/purchases/")

# Aggregation
result = df.groupBy("user_id").agg(
    {"purchase_amount": "sum"}
).withColumnRenamed("sum(purchase_amount)", "total_spent")

result.show()
```

**Input:** Partitioned Parquet files in HDFS.
**Output:** Total spent per user.
**Explanation:** Defining schema improves read performance (no inference pass needed). Parquet is columnar — only the needed columns (`user_id`, `purchase_amount`) are read from disk.

## Common Mistakes
- Using `collect()` on large DataFrames — crashes the driver.
- Not specifying schema for CSV/JSON reads (Spark infers by scanning — slow).
- Confusing `withColumn` (adds/replaces single column) with `select` (projects columns).
- Forgetting that DataFrame operations return new DataFrames — original is unchanged.
- Not filtering before joins — huge shuffle overhead.

## How Interviewers Ask This
- "What is a DataFrame and how is it different from an RDD?"
- "How does Spark Catalyst optimizer improve DataFrame performance?"
- "How do you create a DataFrame from different sources?"
- "What is the difference between `withColumn` and `select`?"
- "How do you handle schema evolution in DataFrames?"

## How To Impress The Interviewer
- Explain that DataFrames are Dataset[Row] in Scala/Java. Python DataFrames are equivalent via Py4J bridge.
- Discuss Catalyst optimizer phases: Analysis → Logical Optimization → Physical Planning → Code Generation (Tungsten).
- Mention that DataFrames support ANSI SQL:2016 — window functions, subqueries, CTEs.
- Talk about the cost model in Catalyst — it estimates data size and chooses join strategies (broadcast hash join vs sort-merge join).
- Describe Tungsten: generates Java bytecode instead of interpreting Spark functions; uses cache-aware algorithms.

## Most Asked Questions

**1. What is a DataFrame in Spark?**
A distributed collection of data organized into named columns. Similar to a table in SQL. Built on top of RDDs with a schema and query optimizer.

**2. How is a DataFrame different from an RDD?**
DataFrames have a schema (column names, types) and use Catalyst optimizer. RDDs are untyped objects. DataFrames are 2-5x faster due to optimized execution.

**3. How do you create a DataFrame?**
- `spark.read.csv(path)`, `spark.read.json(path)`, `spark.read.parquet(path)`
- `spark.createDataFrame(data, schema)`
- `spark.sql("SELECT * FROM table")`
- Converting from RDD: `rdd.toDF(["col1", "col2"])`

**4. What is schema inference in Spark?**
Spark reads a sample of data to determine column types. Useful but slow. Better to provide explicit schema for production.

**5. What is the difference between `withColumn` and `select`?**
`withColumn` adds or replaces one column (keeps existing columns). `select` projects a subset of columns. `withColumn` is convenient for adding derived columns.

**6. What is the difference between `createOrReplaceTempView` and `createGlobalTempView`?**
`createOrReplaceTempView`: Session-scoped — visible only within the current SparkSession. `createGlobalTempView`: Cluster-scoped — visible across sessions.

**7. What are common DataFrame operations?**
`select`, `filter`/`where`, `groupBy`, `agg`, `join`, `union`, `withColumn`, `orderBy`, `drop`, `distinct`.

**8. How does Spark handle null values in DataFrames?**
`df.na.fill(value)` — fill nulls. `df.na.drop()` — drop rows with nulls. `df.filter(df.col.isNotNull())` — filter nulls.

**9. What is the difference between `union` and `unionAll`?**
In Spark 2.0+, `union` = `unionAll` (both keep duplicates). To remove duplicates, use `union().distinct()`. In older versions, `union` removed duplicates.

**10. How do you rename a column in a DataFrame?**
`df.withColumnRenamed("old_name", "new_name")` or `df.selectExpr("old_name as new_name")`.

**11. What is `explain()` in DataFrames?**
Shows the physical execution plan. `df.explain(true)` shows the full logical and physical plans. Essential for debugging and optimization.

**12. How do you convert a DataFrame to an RDD?**
`df.rdd` — returns the underlying RDD of Rows. Note: you lose schema/optimizations.

**13. What is the difference between cache and persist for DataFrames?**
`cache()` = `persist(StorageLevel.MEMORY_AND_DISK)`. `persist()` allows specifying any storage level.

**14. What is the DataFrameWriter?**
Used to write DataFrames to storage. `df.write.csv(path)`, `df.write.parquet(path)`, `df.write.mode("overwrite").save(path)`. Modes: append, overwrite, error, ignore.

**15. How do you handle schema evolution in DataFrames?**
Parquet supports schema evolution — `mergeSchema=true` allows reading files with different schemas. Avro also supports schema evolution.
