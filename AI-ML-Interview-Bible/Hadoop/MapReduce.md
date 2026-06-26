# MapReduce

## What is it?
MapReduce is a programming model for processing large datasets in parallel across a cluster. It has two phases: Map (process key-value pairs and emit intermediate results) and Reduce (aggregate/combine the intermediate results). Hadoop handles parallelization, fault tolerance, and data distribution automatically.

## Why do we use it?
MapReduce abstracts distributed computing complexity. You write two functions (map and reduce) and Hadoop handles splitting data, scheduling tasks, shuffling, sorting, and fault recovery. It processes terabytes of data on hundreds of nodes reliably.

## Real-Time Example 1
**Word Count for Search Engine Indexing:** Google uses MapReduce to count word frequencies across the entire web.
1. Input: Crawled web pages stored as text files in HDFS.
2. Mapper: Each mapper reads a line, splits words, emits (word, 1) pairs.
3. Shuffle & Sort: Hadoop groups all pairs by word across all mappers.
4. Reducer: Sums the counts for each word → (word, total_count).
5. Output: Word frequency table stored in HDFS for search indexing.

## Real-Time Example 2
**Retail Sales Aggregation:** Walmart aggregates daily sales across 5000+ stores.
1. Input: Sales CSV files from all stores (10TB/day).
2. Mapper: Extracts (store_id, sale_amount) from each row.
3. Combiner: Sums amounts per store locally before shuffle — reduces network traffic.
4. Reducer: Receives partial sums, computes final totals per store.
5. Output: Store-wise daily sales report.

## Concept Example 1
**MapReduce Word Count in Python (Hadoop Streaming).**

```python
# mapper.py
import sys
for line in sys.stdin:
    words = line.strip().split()
    for word in words:
        print(f"{word}\t1")

# reducer.py
import sys
current_word = None
current_count = 0
for line in sys.stdin:
    word, count = line.strip().split('\t')
    if word == current_word:
        current_count += int(count)
    else:
        if current_word:
            print(f"{current_word}\t{current_count}")
        current_word = word
        current_count = int(count)
if current_word:
    print(f"{current_word}\t{current_count}")
```

**Input:** "hello world hello hadoop hello world"
**Output:** hadoop 1, hello 3, world 2
**Explanation:** Mapper emits each word with 1. Reducer sums values with the same key. Hadoop Streaming lets you write MapReduce in Python, not just Java.

## Concept Example 2
**MapReduce with Combiner (same logic, improved performance).**

```python
# combiner.py (can be the same as reducer)
import sys
current_word = None
current_count = 0
for line in sys.stdin:
    word, count = line.strip().split('\t')
    if word == current_word:
        current_count += int(count)
    else:
        if current_word:
            print(f"{current_word}\t{current_count}")
        current_word = word
        current_count = int(count)
if current_word:
    print(f"{current_word}\t{current_count}")
```

**Input:** 100GB of text across 50 DataNodes.
**Output:** Word frequencies computed faster because the combiner reduces data sent over the network.
**Explanation:** The combiner runs on the mapper side after the map phase. It aggregates partial results locally, reducing the data volume that must be shuffled across the network.

## Common Mistakes
- Thinking the Combiner is guaranteed to run (Hadoop decides whether to invoke it). It should be idempotent.
- Writing reducers that expect sorted keys globally — they are sorted per reducer partition, not globally.
- Ignoring data skew — one reducer gets most of the data (e.g., a single super-common word).
- Not setting the number of reducers correctly (too many = small files, too few = long runtime).

## How Interviewers Ask This
- "Explain the full MapReduce flow from input to output."
- "What is a Combiner and when would you use it?"
- "How does the Partitioner work? Can you write a custom one?"
- "What is the shuffle phase? Why is it expensive?"
- "How do you handle skewed data in MapReduce?"

## How To Impress The Interviewer
- Explain that shuffle + sort is the most expensive phase — it involves serialization, disk I/O, and network transfer.
- Discuss speculative execution: Hadoop launches duplicate tasks on slow nodes (stragglers).
- Mention Uber tasks: small jobs run entirely on one node to avoid scheduling overhead.
- Talk about the `Partitioner` interface — default is HashPartitioner; custom partitioning can solve skew.
- Explain how `MapReduce v2` (YARN) decouples compute from HDFS, allowing other engines (Spark, Tez).

## Most Asked Questions

**1. What is MapReduce and how does it work?**
MapReduce is a distributed processing framework. It takes input as key-value pairs, applies a map function to produce intermediate pairs, shuffles/sorts by key, and reduces to produce final output.

**2. What is the difference between Map and Reduce?**
Map processes input records in parallel and emits intermediate (key, value) pairs. Reduce groups all values for a given key and aggregates them into final output.

**3. What is a Combiner?**
A Combiner is a mini-reducer that runs on the mapper node after the map phase. It reduces data volume before shuffle. It must be commutative and associative (same logic as reducer).

**4. What is a Partitioner?**
A Partitioner determines which reducer a key-value pair goes to. Default is HashPartitioner (hash(key) % numReducers). You can write a custom Partitioner for range-based or skewed data.

**5. What is the shuffle and sort phase?**
Shuffle: Mapper outputs are fetched by reducers over the network. Sort: Keys are sorted within each reducer partition. This is the most expensive phase.

**6. What happens if a mapper fails?**
Hadoop re-runs the failed mapper task on another node. If the same mapper fails repeatedly (default: 4 times), the job fails.

**7. How does MapReduce handle data locality?**
MapReduce tries to schedule mapper tasks on the same node (or rack) where the input data resides. This minimizes network transfer.

**8. What is speculative execution?**
Hadoop launches duplicate tasks for slow workers (stragglers). Whichever finishes first is accepted; the other is killed.

**9. What is the role of InputFormat and OutputFormat?**
InputFormat splits input data into splits and provides RecordReader to parse records. OutputFormat writes results to the output destination. Common ones: TextInputFormat, KeyValueTextInputFormat, SequenceFileInputFormat.

**10. How do you control the number of reducers?**
Set `mapreduce.job.reduces` property. Default is 1. A good rule: 0.9–1.1 × (cluster nodes × reducer container slots).

**11. What is a MapReduce job?**
A full execution unit consisting of input data, map/reduce functions, and configuration. It runs across cluster nodes.

**12. Can MapReduce run without reducers?**
Yes. Map-only jobs are common (e.g., data cleaning, format conversion, ETL). Set `mapreduce.job.reduces=0`.

**13. What is the difference between MapReduce v1 and v2?**
MRv1 had JobTracker and TaskTracker. MRv2 runs on YARN — ResourceManager manages resources, ApplicationMaster handles job execution. MRv2 is more scalable.

**14. How does serialization work in MapReduce?**
Hadoop uses Writable interface for serialization. WritableComparable enables both serialization and sorting. Common types: IntWritable, Text, LongWritable.

**15. What is the purpose of the setup() and cleanup() methods?**
`setup()` runs once before map/reduce tasks start (e.g., open a database connection). `cleanup()` runs once after all map/reduce calls (e.g., close connections).
