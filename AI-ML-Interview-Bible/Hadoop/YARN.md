# YARN (Yet Another Resource Negotiator)

## What is it?
YARN is Hadoop's cluster resource management layer. It decouples resource management (CPU, memory) from job scheduling/monitoring. The key components are ResourceManager (global master), NodeManager (per-node agent), and ApplicationMaster (per-application coordinator).

## Why do we use it?
YARN enables multiple processing engines (MapReduce, Spark, Flink, Tez) to share the same cluster resources. It improves cluster utilization by dynamically allocating containers based on demand. It replaced the monolithic JobTracker in Hadoop 1.x.

## Real-Time Example 1
**Multi-Tenant Data Platform:** A company runs MapReduce (nightly ETL), Spark (real-time streaming), and interactive Hive queries on the same YARN cluster.
1. ResourceManager allocates 60% of cluster resources to the MapReduce ETL queue.
2. A Spark streaming job requests containers for micro-batch processing.
3. YARN dynamically provides containers from available resources.
4. When ETL finishes, resources are reclaimed and given to Hive or Spark.
5. All engines share resources without dedicated clusters.

## Real-Time Example 2
**Retail Flash Sale Processing:** During Black Friday, an e-commerce site runs inventory processing using Spark on YARN.
1. ResourceManager allocates containers on multiple nodes.
2. ApplicationMaster negotiates for 200 containers (4 CPU, 8GB each).
3. Spark executors start in these containers and process sale data.
4. If resources are constrained, YARN queues the request.
5. After the sale, resources are released back to the cluster.

## Concept Example 1
**Submitting a Spark job to YARN cluster.**

```bash
spark-submit \
  --master yarn \
  --deploy-mode cluster \
  --num-executors 10 \
  --executor-cores 4 \
  --executor-memory 8g \
  --queue production \
  s3://jars/etl-pipeline.py
```

**Input:** Spark application code and resource requests.
**Output:** YARN allocates 10 containers (each 4 cores, 8GB) and runs the application.
**Explanation:** `--master yarn` tells Spark to use YARN's ResourceManager. `--deploy-mode cluster` means the driver runs inside a YARN container. YARN manages container lifecycle.

## Concept Example 2
**Viewing YARN resource usage.**

```bash
# List all applications
yarn application -list

# View cluster resource status
yarn node -list

# Kill a stuck application
yarn application -kill application_123456789_0001

# View application logs
yarn logs -applicationId application_123456789_0001
```

**Input:** `yarn node -list` command.
**Output:** List of all NodeManagers with their total/used memory and vCores.
**Explanation:** YARN CLI provides visibility into cluster resources. This is essential for debugging resource contention and monitoring utilization.

## Common Mistakes
- Not configuring `yarn.scheduler.minimum-allocation-mb` — containers get tiny memory, jobs fail.
- Setting `yarn.nodemanager.resource.memory-mb` higher than actual RAM — causes OOM kills.
- Not enabling preemption — high-priority jobs wait behind low-priority ones.
- Confusing `virtual cores (vCores)` with physical cores. vCores are YARN's CPU abstraction.

## How Interviewers Ask This
- "Explain YARN architecture and its components."
- "What is the difference between YARN and MapReduce v1 JobTracker?"
- "How does YARN schedule containers?"
- "What happens when the ResourceManager fails?"
- "How does YARN support multiple data processing engines?"

## How To Impress The Interviewer
- Explain the three schedulers: FIFO, Capacity Scheduler, Fair Scheduler. Capacity Scheduler is most common in production.
- Discuss YARN container placement — it tries local (same node), then rack-local, then off-rack.
- Mention YARN node labels — assign nodes to specific applications (e.g., GPU nodes for deep learning).
- Talk about the ResourceManager's state store (ZooKeeper, HDFS, LevelDB) for HA.
- Explain container sizing strategy: don't set max containers too high — overhead from JVM and OS reduces available resources.

## Most Asked Questions

**1. What is YARN?**
YARN is Hadoop's cluster resource management framework. It manages CPU and memory across cluster nodes and schedules containers for various data processing engines.

**2. Explain the main components of YARN.**
- **ResourceManager (RM):** Global master; allocates resources, tracks cluster state.
- **NodeManager (NM):** Per-node agent; manages containers, reports status to RM.
- **ApplicationMaster (AM):** Per-application coordinator; negotiates containers, monitors progress.

**3. What is a container in YARN?**
A container is a logical bundle of resources (CPU cores, memory) allocated by YARN. Application code runs inside containers.

**4. What is the difference between YARN and Mesos?**
YARN uses fine-grained scheduling (ResourceManager decides allocations). Mesos offers coarse-grained offers (frameworks accept/reject). YARN is tighter integrated with Hadoop.

**5. How does YARN achieve high availability?**
ResourceManager HA uses ZooKeeper for leader election and a state store (ZooKeeper, HDFS, or LevelDB) to persist application state.

**6. What is the YARN application lifecycle?**
1. Client submits application to RM. 2. RM launches AM in a container. 3. AM requests more containers. 4. Tasks run in containers. 5. AM reports completion to RM. 6. RM cleans up.

**7. What are the different YARN schedulers?**
- **FIFO:** First-in, first-out (simple but no fair sharing).
- **Capacity Scheduler:** Guaranteed capacity per queue; pre-configured limits.
- **Fair Scheduler:** Dynamic fair sharing; short jobs get resources quickly.

**8. How does YARN isolate resources?**
Linux Container Executor (LCE) or Docker containers isolate CPU, memory, and processes. CGroups limit resource usage at the OS level.

**9. What happens when a NodeManager fails?**
ResourceManager marks the node as dead. Containers on that node are re-attempted on other nodes. ApplicationMaster handles task recovery.

**10. What is the role of ApplicationMaster?**
AM negotiates containers with RM, tracks task progress, handles failures, and releases containers when done. Each application has its own AM.

**11. How do you configure YARN memory?**
Set `yarn.nodemanager.resource.memory-mb` (total RAM per node), `yarn.scheduler.minimum-allocation-mb` (min container size), `yarn.scheduler.maximum-allocation-mb` (max container size).

**12. What is container preemption?**
YARN can kill low-priority containers to allocate resources to high-priority ones. Requires `yarn.resourcemanager.scheduler.monitor.enable=true`.

**13. How does YARN handle oversubscription?**
YARN allows oversubscription of CPU (not memory) through `yarn.nodemanager.resource.cpu-vcores`. CPU is compressible — tasks can share cores.

**14. What is node labeling in YARN?**
Assign labels to nodes (e.g., "GPU", "SSD", "high-mem"). Applications request specific node labels. Useful for specialized workloads.

**15. How does Spark run on YARN?**
Two modes: `yarn-client` (driver runs on client machine) and `yarn-cluster` (driver runs in a YARN container). `yarn-cluster` is preferred for production.
