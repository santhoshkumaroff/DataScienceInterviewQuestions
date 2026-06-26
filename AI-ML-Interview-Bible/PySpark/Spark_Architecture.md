# Spark Architecture

## What is it?
Apache Spark is a unified analytics engine for large-scale data processing. It uses a master-slave architecture: the Driver (master) runs the main program and creates a DAG (Directed Acyclic Graph) of stages, while Executors (slaves) run tasks on worker nodes. The Cluster Manager (YARN, Mesos, Kubernetes, or standalone) allocates resources.

## Why do we use it?
Spark is up to 100x faster than MapReduce for in-memory processing. It provides unified APIs (SQL, streaming, MLlib, GraphX) and supports batch, interactive, and streaming workloads in a single engine. Its DAG scheduler optimizes execution plans automatically.

## Real-Time Example 1
**Real-Time Fraud Detection:** A bank detects credit card fraud using Spark Streaming.
1. Transaction events (5000/sec) arrive from Kafka.
2. Spark Driver creates a DAG: read from Kafka → parse → feature extraction → ML prediction.
3. Executors run tasks in parallel across 20 nodes.
4. The DAG Scheduler pipelines operations for efficiency.
5. Fraud alerts are sent within 100ms per transaction.

## Real-Time Example 2
**Ad-Hoc Analytics Platform:** A media company uses Spark SQL for interactive dashboards.
1. Data analysts submit SQL queries via JDBC/ODBC to Spark Thrift Server.
2. Spark's Catalyst Optimizer rewrites the query plan.
3. Tungsten generates efficient bytecode for execution.
4. Executors scan Parquet files in HDFS with predicate pushdown.
5. Results return in seconds for terabytes of data.

## Concept Example 1
**Understanding the SparkContext and SparkSession.**

```python
from pyspark.sql import SparkSession

# Create SparkSession (entry point in Spark 2.0+)
spark = SparkSession.builder \
    .appName("MyApp") \
    .master("yarn") \
    .config("spark.executor.memory", "4g") \
    .getOrCreate()

# The SparkContext is accessible via spark.sparkContext
print(f"App ID: {spark.sparkContext.applicationId}")
print(f"Default parallelism: {spark.sparkContext.defaultParallelism}")
```

**Input:** Configuration for the Spark application.
**Output:** SparkSession is created with specified configs; app runs on YARN with 4GB/executor.
**Explanation:** SparkSession is the unified entry point. It encapsulates SparkContext (RDD API), SQLContext, HiveContext, and StreamingContext.

## Concept Example 2
**Understanding the DAG execution flow.**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("DAGExample").getOrCreate()

# Read data
df = spark.read.parquet("/user/data/sales.parquet")

# Transformations (build DAG)
filtered = df.filter(df.amount > 100)
grouped = filtered.groupBy("category").sum("amount")

# Action (triggers DAG execution)
result = grouped.collect()
```

**Input:** Data in HDFS (`sales.parquet`).
**Output:** Filtered, grouped results collected to the driver.
**Explanation:** No computation happens until `collect()` — Spark builds a DAG of stages. The DAG Scheduler splits the graph into stages (based on shuffle boundaries) and tasks (one per partition).

## Common Mistakes
- Setting executor memory too high (causes GC overhead or failed container requests).
- Not understanding that the Driver is a single point of failure in non-HA mode.
- Creating a new SparkSession inside loops — should create once and reuse.
- Collecting large datasets to the driver (memory overflow).

## How Interviewers Ask This
- "Explain the Spark execution architecture."
- "What is the role of the DAG Scheduler?"
- "How does Spark differ from MapReduce in execution?"
- "What happens when an executor fails?"
- "Explain Spark's memory management."

## How To Impress The Interviewer
- Describe the DAG Scheduler vs Task Scheduler. DAG Scheduler creates stages (narrow transforms pipelined; wide transforms cause stage boundaries). Task Scheduler launches tasks onto executors.
- Explain Spark Memory Model: Reserved (300MB), User Memory (stores data structures), Execution Memory (shuffle, joins), Storage Memory (cache).
- Discuss dynamic allocation: Spark can scale executors up/down based on load (`spark.dynamicAllocation.enabled=true`).
- Mention the two deploy modes: `client` (driver runs on the submission machine) and `cluster` (driver runs on a worker node).

## Most Asked Questions

**1. What is Apache Spark and how does it work?**
Spark is a distributed data processing engine. It uses a Driver to coordinate tasks across Executors. It processes data in-memory using a DAG execution engine for performance.

**2. What are the components of Spark architecture?**
- **Driver:** Main program — creates SparkContext, defines DAG, schedules tasks.
- **Executors:** Worker processes — run tasks, store cached data, return results.
- **Cluster Manager:** YARN, Mesos, Kubernetes, or Standalone — allocates resources.

**3. What is the difference between Spark and MapReduce?**
Spark processes data in-memory (up to 100x faster for iterative jobs). MapReduce writes intermediate data to disk. Spark has a unified API (SQL, ML, streaming); MapReduce only has map/reduce.

**4. What is a DAG in Spark?**
A Directed Acyclic Graph of computation stages. Spark builds a DAG from transformations. The DAG Scheduler converts it into physical execution stages and tasks.

**5. Explain the job, stage, and task in Spark.**
- **Job:** Triggered by an action (collect, count, save). Corresponds to one DAG.
- **Stage:** Set of tasks without shuffle. Shuffle causes stage boundary.
- **Task:** Smallest unit of work — one task per partition per stage.

**6. How does Spark handle executor failure?**
Tasks on failed executors are re-scheduled to other executors. For cached partitions, lineage rebuilds the data. The driver reapplies transformations to recompute lost data.

**7. What is the difference between SparkContext and SparkSession?**
SparkContext (pre-Spark 2.0) was the main entry point. SparkSession (2.0+) unifies SparkContext, SQLContext, HiveContext, StreamingContext. Prefer SparkSession.

**8. What is the role of the Cluster Manager?**
Allocates resources (CPU, memory) to Spark applications. Independent of Spark — supports YARN, Mesos, Kubernetes, or Standalone (built-in).

**9. Explain client vs cluster deploy mode.**
- **Client:** Driver runs on the machine that submits the application. Client is responsible for driver.
- **Cluster:** Driver runs inside a YARN container or Kubernetes pod. Cluster manager manages the driver.

**10. What is dynamic allocation in Spark?**
Spark can add/remove executors based on workload. Enabled with `spark.dynamicAllocation.enabled=true`. Useful for resource efficiency.

**11. How does Spark manage memory?**
- **Reserved Memory:** 300MB for Spark internals.
- **User Memory:** For user-defined data structures.
- **Execution Memory:** For shuffles, joins, aggregations.
- **Storage Memory:** For cached data (RDD/DataFrame).

**12. What is the Spark Thrift Server?**
A JDBC/ODBC server that allows external tools (Tableau, Power BI) to run Spark SQL queries. It reuses SparkSession across multiple queries.
