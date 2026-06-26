# HDFS (Hadoop Distributed File System)

## What is it?
HDFS is a distributed file system designed to store massive data across multiple machines. It splits files into blocks (default 128MB) and replicates them across the cluster. It follows a master-slave architecture with NameNode as master and DataNodes as slaves.

## Why do we use it?
HDFS enables reliable storage of petabytes of data on commodity hardware. It provides fault tolerance through replication, high throughput for batch processing, and data locality for MapReduce jobs. It's the foundation of the Hadoop ecosystem.

## Real-Time Example 1
**E-commerce Log Storage:** An e-commerce company like Amazon generates terabytes of clickstream logs daily.
1. Click events are streamed to HDFS from web servers.
2. HDFS splits the raw log file into 128MB blocks.
3. Each block is replicated 3 times across different DataNodes and racks.
4. If a DataNode fails, the NameNode detects it and replicates the blocks from remaining copies.
5. Analysts run Hive/Pig jobs directly on the data without moving it.

## Real-Time Example 2
**Healthcare Image Storage:** A hospital chain stores millions of MRI and CT scan images.
1. Images (500MB–1GB each) are ingested into HDFS.
2. HDFS stores them as large block files with high replication (replication factor 3).
3. Rack awareness ensures data survives an entire rack failure.
4. Radiologists use distributed processing tools to query and analyze images.

## Concept Example 1
**Writing a file to HDFS and checking block details.**

```bash
# Create a directory
hdfs dfs -mkdir /user/data/logs

# Copy a 500MB file to HDFS
hdfs dfs -put weblog_2025-01-01.log /user/data/logs/

# Check file block information
hdfs fsck /user/data/logs/weblog_2025-01-01.log -files -blocks -locations
```

**Input:** A 500MB log file.
**Output:** The file is split into 4 blocks (128MB each), each replicated 3 times across DataNodes.
**Explanation:** HDFS divides large files into blocks. Each block is stored independently. The `fsck` command shows block locations for debugging data locality.

## Concept Example 2
**Reading a file from HDFS in Python.**

```python
from hdfs import InsecureClient

client = InsecureClient('http://namenode:50070', user='hadoop')

with client.read('/user/data/logs/weblog_2025-01-01.log') as reader:
    content = reader.read()
    print(f"Read {len(content)} bytes from HDFS")
```

**Input:** HDFS file path.
**Output:** File content streamed to the application.
**Explanation:** The HDFS client abstracts away block locations. It contacts the NameNode for metadata, then reads directly from DataNodes.

## Common Mistakes
- Setting replication factor too high (wastes storage) or too low (risk of data loss).
- Not understanding that NameNode is a single point of failure (HA with QJM solves this).
- Using HDFS for small files (lots of small files = NameNode memory bloat).
- Forgetting to check `dfs.datanode.du.reserved` — disks fill up and DataNodes stop.

## How Interviewers Ask This
- "What happens when the NameNode goes down?"
- "How does HDFS handle a DataNode failure?"
- "What is the default block size and why is it large (128MB/256MB)?"
- "Explain rack awareness and its benefits."
- "What is the difference between HDFS and traditional NFS?"

## How To Impress The Interviewer
- Explain that block size is large to minimize seek time vs transfer time ratio. HDFS is optimized for streaming reads, not random access.
- Discuss NameNode HA using Quorum Journal Manager (QJM) with 3 or 5 JournalNodes.
- Mention Erasure Coding (available in HDFS 3.x) — saves 50% storage compared to 3x replication but uses more CPU.
- Bring up HDFS Federation for horizontal scalability of the namespace.
- Talk about `fsimage` and `edits` logs for NameNode metadata persistence.

## Most Asked Questions

**1. What is HDFS and what are its core components?**
HDFS is a distributed file system. Core components: NameNode (manages metadata), DataNode (stores blocks), Secondary NameNode (checkpoints edits log).

**2. What is the default block size in HDFS?**
128MB in Hadoop 2.x and 3.x. In Hadoop 1.x it was 64MB. Large block size reduces seek overhead.

**3. How does HDFS handle fault tolerance?**
Data is replicated across nodes (default replication factor = 3). If a DataNode fails, the NameNode replicates missing blocks from surviving copies.

**4. What is rack awareness?**
The NameNode knows which rack each DataNode belongs to. It places replicas across racks to survive rack-level failures. Typical placement: 2 replicas on one rack, 1 on another.

**5. What is the difference between NameNode and Secondary NameNode?**
NameNode manages metadata and handles client requests. Secondary NameNode periodically merges `fsimage` and `edits` logs to prevent the edits log from growing unbounded. It is NOT a hot standby.

**6. What happens when the NameNode fails?**
Without HA, the cluster goes down. All metadata is lost unless the fsimage and edits are backed up. With HA, a Standby NameNode takes over within seconds.

**7. What is HDFS Federation?**
Multiple NameNodes manage different namespaces independently. Each NameNode manages a set of block pools. DataNodes register with all NameNodes and send heartbeats to all.

**8. How does HDFS handle small files?**
Small files are inefficient because each file consumes NameNode memory (~150 bytes per block). Many small files = excessive metadata. Solution: Combine small files into sequence files or use HBase.

**9. What is the write pipeline in HDFS?**
Client writes to the first DataNode, which forwards to the second, which forwards to the third. Acknowledgments flow back. If a DataNode fails, the pipeline is reconstructed.

**10. What is HDFS Erasure Coding?**
Introduced in HDFS 3.x. Instead of 3x replication, it uses Reed-Solomon encoding (e.g., 6 data + 3 parity blocks). Saves ~50% storage but uses more CPU for reconstruction.

**11. How do you read a file from HDFS?**
Client contacts NameNode for block locations, then reads blocks directly from DataNodes in parallel.

**12. What is safemode in HDFS?**
NameNode starts in safemode where it does not accept write operations. It receives block reports from DataNodes and exits safemode when the threshold of replicated blocks is met.
