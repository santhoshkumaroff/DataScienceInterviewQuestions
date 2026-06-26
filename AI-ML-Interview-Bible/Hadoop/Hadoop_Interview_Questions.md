# Hadoop Interview Questions (25+ Questions)

## Core Hadoop

**1. What is Hadoop and what are its core components?**
Hadoop is an open-source framework for distributed storage and processing of large datasets. Core components: HDFS (storage), MapReduce (processing), YARN (resource management), and Common (utilities).

**2. What are the advantages of Hadoop over traditional databases?**
- Scales horizontally by adding commodity hardware.
- Handles unstructured/semi-structured data (logs, images, text).
- Fault-tolerant — data is replicated across nodes.
- Cost-effective compared to expensive proprietary storage.

**3. What is the difference between Hadoop 1.x and Hadoop 2.x/3.x?**
Hadoop 1.x had JobTracker + TaskTracker (single point of failure, limited scalability). Hadoop 2.x introduced YARN (ResourceManager + NodeManager) for better resource management and multi-engine support. Hadoop 3.x added Erasure Coding, multiple NameNodes, and Java 8+ support.

**4. What is data locality in Hadoop?**
Moving computation to data rather than data to computation. MapReduce tasks are scheduled on nodes where the input data blocks reside, minimizing network traffic.

**5. Explain Hadoop Rack Awareness.**
The NameNode knows the network topology (rack IDs). It places replicas across racks to survive rack failures. Default replica placement: 1st replica on local rack, 2nd on different node in same rack, 3rd on a different rack.

**6. What is the Hadoop ecosystem?**
The set of tools built around Hadoop: Hive (SQL), Pig (scripting), HBase (NoSQL), Spark (fast processing), Flume (ingestion), Sqoop (RDBMS import/export), Oozie (scheduling), ZooKeeper (coordination), Kafka (streaming).

**7. What is the difference between NameNode and DataNode?**
NameNode manages filesystem metadata (names, blocks, locations). DataNode stores actual data blocks and serves read/write requests.

**8. What is the default replication factor and why?**
Default is 3. Trade-off between fault tolerance (more replicas = safer) and storage cost. 3 replicas tolerates 2 node failures.

**9. How do you handle a failed DataNode?**
NameNode detects missing heartbeats (default 10 min). Blocks from the dead node are marked under-replicated and new replicas are created on active nodes.

**10. What is SafeMode in HDFS?**
NameNode starts in SafeMode — read-only, no writes. It collects block reports from DataNodes. Exits SafeMode when >99.9% of blocks are replicated.

## MapReduce

**11. Explain the complete MapReduce flow.**
1. Input split: InputFormat splits input into logical splits.
2. Map: Each split is processed by a mapper — emits (key, value) pairs.
3. Combine (optional): Local aggregation on mapper node.
4. Partition: Determines which reducer gets each key.
5. Shuffle & Sort: Mapper outputs are copied to reducers, sorted by key.
6. Reduce: Reducer aggregates values for each key.
7. Output: OutputFormat writes results to HDFS.

**12. What is a Combiner and when is it useful?**
A Combiner performs local aggregation on mapper outputs before shuffle. Useful when the reduce function is commutative/associative (SUM, COUNT, MAX). Reduces network transfer.

**13. What happens if the map output key is not WritableComparable?**
MapReduce cannot sort the keys. The job will fail. All keys and values must implement Writable (or WritableComparable for keys).

**14. How does MapReduce handle failures?**
Task failure: Re-attempted up to 4 times. TaskTracker/NodeManager failure: Tasks reassigned to other nodes. Job failure: JobTracker/ResourceManager is notified.

**15. What is speculative execution in MapReduce?**
Duplicating slow tasks on other nodes to combat stragglers. The first to finish wins; others are killed. Controlled by `mapreduce.map.speculative` and `mapreduce.reduce.speculative`.

## YARN

**16. Explain YARN architecture.**
ResourceManager (RM): Global scheduler. NodeManager (NM): Per-node agent managing containers. ApplicationMaster (AM): Per-application process negotiating containers and tracking tasks.

**17. What is the difference between YARN and standalone Spark?**
YARN: ResourceManager manages resources across the cluster for multiple engines. Standalone Spark: Spark's built-in cluster manager. YARN is preferred in production for multi-engine environments.

**18. How does YARN allocate containers?**
ApplicationMaster sends resource requests (memory, CPU) to ResourceManager. RM schedules containers based on available resources, queue policies, and scheduling algorithm (Capacity/Fair/FIFO).

**19. What happens when a YARN ApplicationMaster fails?**
ResourceManager restarts the AM on another node. The AM recovers task state from HDFS or ZooKeeper (if configured). Max retries: `yarn.resourcemanager.am.max-attempts` (default 2).

**20. What are YARN node labels?**
Labels (e.g., "GPU", "highmem") assigned to nodes. Applications specify required labels in resource requests. Useful for specialized hardware isolation.

## Hive

**21. What is the difference between partitioning and bucketing?**
Partitioning divides data into directories by column value (e.g., date). Bucketing divides data into fixed files using hash of a column. Partitioning reduces scan range; bucketing improves JOIN and sampling.

**22. How does Hive execute a query?**
1. Hive parses SQL → AST (Abstract Syntax Tree). 2. Compiler generates execution plan. 3. Optimizer (CBO) improves plan. 4. Executor runs the plan on Tez/MapReduce/Spark.

**23. What file format is best for Hive and why?**
ORC (Optimized Row Columnar). It offers column pruning, predicate pushdown, built-in indexes, and best compression (up to 75% savings). Parquet is a close second with broader platform support.

**24. What is the difference between dynamic and static partitioning?**
Static: You specify the partition value explicitly. Dynamic: Hive determines partition values from the data. Dynamic partitioning requires `hive.exec.dynamic.partition.mode=nonstrict`.

**25. How do you handle data skew in Hive?**
- Salting the key: Append random value to skewed keys before GROUP BY/JOIN.
- Use `hive.groupby.skewindata=true` — splits skewed GROUP BY into two MapReduce jobs.
- Bucket map joins for skewed JOIN keys.

## General

**26. What is the limitation of HDFS?**
- Not good for low-latency data access (use HBase).
- Small files problem (NameNode memory limits).
- Not POSIX compliant (append-only, no random writes).
- Rename is expensive for directories with many files.

**27. How does Hadoop achieve fault tolerance?**
- HDFS: Block replication (default 3x), rack awareness, NameNode HA.
- MapReduce: Task re-execution on failure, speculative execution.
- YARN: ApplicationMaster recovery, NodeManager heartbeat monitoring.

**28. What is the role of secondary NameNode? NOT a standby NameNode. It periodically merges fsimage and edits log to keep the edits file from growing infinitely. Only for checkpointing.

**29. Explain Hadoop deployment modes.**
- **Standalone (Local):** Runs as a single Java process — no HDFS. For debugging.
- **Pseudo-distributed:** Each daemon runs in its own JVM on one machine.
- **Fully distributed:** Multiple nodes; production setup with HA.

**30. What is the Hadoop Distributed Copy (DistCp)?**
A tool for large-scale inter/intra-cluster data copying. Uses MapReduce for parallel copy. Supports bandwidth limiting, overwrite, update. Command: `hadoop distcp hdfs://source hdfs://dest`

**31. What is the difference between block compression and file compression?**
Block compression: Each block is compressed independently, splittable (BZip2, LZO). File compression: Entire file compressed together, NOT splittable (GZip, Snappy). For MapReduce, use splittable compression.

**32. What tools are used for data ingestion in Hadoop?**
- Flume: Streaming data (logs, events).
- Sqoop: Bulk transfer between RDBMS and HDFS.
- Kafka: Publish-subscribe messaging.
- NiFi: GUI-based data flow management.

**33. How does Hadoop authentication work?**
Kerberos is the default authentication mechanism. Hadoop supports: Simple (no auth), Kerberos (strong), LDAP/AD integration via PAM. Authorization via ACLs and Ranger/Sentry.

**34. What is ZooKeeper's role in Hadoop?**
Used for: NameNode HA (leader election), ResourceManager HA, HBase meta-region management, distributed coordination (locks, configuration).

**35. How do you troubleshoot a slow MapReduce job?**
Check: Data skew, too few reducers, speculative execution disabled, insufficient memory, straggler nodes, excessive shuffling, small files, lack of compression.
