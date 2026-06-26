# Multithreading

## What is it?
Multithreading runs multiple threads (lightweight tasks) within one process. A thread is the smallest unit of execution. The GIL (Global Interpreter Lock) prevents multiple threads from executing Python bytecode simultaneously. For CPU-bound tasks, use multiprocessing (separate processes). For I/O-bound tasks, use threading or asyncio (async/await for concurrent I/O). Race conditions happen when threads access shared data without synchronization.

## Why do we use it?
Real-world applications need to handle multiple tasks simultaneously: serving web requests, downloading files, processing data. Understanding concurrency is critical for production systems. In AI/ML, you might parallelize data loading, preprocess batches, or serve multiple model predictions.

## Real-Time Example 1
**Web Scraping (Data Collection)**

Scenario: A data science team needs to scrape 1000 websites for product prices.

Step 1: Create a thread pool of 10 workers.
Step 2: Each thread scrapes one URL at a time (I/O-bound — waiting for network).
Step 3: Threads share a queue of URLs (thread-safe queue).
Step 4: Each thread writes results to a shared list (use lock to avoid race condition).
Step 5: GIL is not a problem because I/O operations release the GIL.

```python
import threading
import queue
import time

def scrape_worker(q, results, lock):
    while not q.empty():
        url = q.get()
        time.sleep(0.1)  # simulate network request
        with lock:
            results.append(f"Scraped: {url}")

urls = [f"http://site{i}.com" for i in range(20)]
q = queue.Queue()
for url in urls: q.put(url)

results = []
lock = threading.Lock()
threads = [threading.Thread(target=scrape_worker, args=(q, results, lock)) for _ in range(5)]
for t in threads: t.start()
for t in threads: t.join()
print(f"Scraped {len(results)} URLs")
```

## Real-Time Example 2
**Model Inference Server (ML/AI)**

Scenario: An ML model server needs to handle 100 prediction requests per second.

Step 1: Use threading to handle multiple API requests concurrently.
Step 2: Each thread loads a model instance or uses shared model.
Step 3: If model is CPU-heavy, use multiprocessing instead of threading (GIL issue).
Step 4: Use asyncio for non-blocking I/O (reading input data).
Step 5: Use locks when updating usage statistics (shared resource).

```python
import asyncio
import time

async def predict(input_data):
    await asyncio.sleep(0.01)  # simulate model inference
    return f"prediction for {input_data}"

async def handle_requests(requests):
    tasks = [predict(r) for r in requests]
    return await asyncio.gather(*tasks)

requests = ["data1", "data2", "data3"]
results = asyncio.run(handle_requests(requests))
print(results)
```

## Concept Example 1
**Threading Basics and Race Condition**

```python
import threading

# Without lock — race condition!
counter = 0
def increment():
    global counter
    for _ in range(100000):
        counter += 1  # not atomic!

threads = [threading.Thread(target=increment) for _ in range(2)]
for t in threads: t.start()
for t in threads: t.join()
print(f"Expected: 200000, Got: {counter}")  # Different each time!
```

Input: None.
Output:
```
Expected: 200000, Got: 134567 (varies)
```
Explanation: `counter += 1` is NOT atomic — it reads, adds, writes. Two threads can read the same value, both increment, and write back the same result. Use Lock to fix.

## Concept Example 2
**Asyncio Basics**

```python
import asyncio

async def fetch_data(name, delay):
    print(f"Fetching {name}...")
    await asyncio.sleep(delay)  # non-blocking wait
    print(f"Done {name}")
    return f"{name}_data"

async def main():
    tasks = [
        fetch_data("A", 2),
        fetch_data("B", 1),
        fetch_data("C", 3),
    ]
    results = await asyncio.gather(*tasks)
    print(results)

asyncio.run(main())
```

Input: None.
Output:
```
Fetching A...
Fetching B...
Fetching C...
Done B
Done A
Done C
['A_data', 'B_data', 'C_data']
```
Explanation: All tasks start immediately. Task B takes 1 second so finishes first. Tasks run concurrently (not in parallel). `await` yields control to the event loop.

## Common Mistakes
1. Trying to use threading for CPU-bound tasks (GIL limits to one CPU core).
2. Not using locks when accessing shared data (race conditions).
3. Creating too many threads (context switching overhead).
4. Forgetting to call `.start()` or `.join()` on threads.
5. Mixing asyncio code with blocking calls (blocks entire event loop).

## How Interviewers Ask This
1. "What is the GIL? How does it affect Python threading?"
2. "What is the difference between threading and multiprocessing?"
3. "What is a race condition? How do you fix it?"
4. "What is asyncio? How does it differ from threading?"
5. "How do you share data between threads safely?"

## How To Impress The Interviewer
1. Explain that GIL is released during I/O operations and C extension calls (like NumPy).
2. Know that multithreading is suitable for I/O-bound tasks (web scraping, file I/O, network calls).
3. Mention `ThreadPoolExecutor` and `ProcessPoolExecutor` from `concurrent.futures`.
4. Understand that asyncio uses cooperative multitasking (tasks yield with `await`), threading uses preemptive multitasking (OS decides).
5. Know about deadlocks (two threads waiting for each other's locks) and use `threading.RLock` for reentrant locks.

## Most Asked Questions

### Q1: What is the GIL?
Global Interpreter Lock — a mutex that allows only one thread to execute Python bytecode at a time. It simplifies CPython memory management but limits CPU-bound parallelism. GIL is released during I/O and in C extensions.

### Q2: What is the difference between threading and multiprocessing?
- Threading: threads share same memory, lightweight, GIL-bound for CPU work, good for I/O.
- Multiprocessing: separate processes, each has own GIL, heavier, true parallelism for CPU work.
```python
from multiprocessing import Process
p = Process(target=func)
```

### Q3: What is a race condition?
When two threads access shared data simultaneously and the result depends on timing. Fix with locks.
```python
lock = threading.Lock()
with lock:
    counter += 1  # protected
```

### Q4: What is asyncio?
A library for writing concurrent code using `async`/`await` syntax. Single-threaded but can handle many I/O tasks concurrently. Best for high-concurrency I/O-bound applications.
Real example: Web servers handling thousands of connections.

### Q5: What is the difference between concurrency and parallelism?
- Concurrency: multiple tasks making progress (interleaved execution) — threading, asyncio.
- Parallelism: multiple tasks running simultaneously (multiple CPU cores) — multiprocessing.

### Q6: How do you create a thread?
```python
import threading
t = threading.Thread(target=my_function, args=(param,))
t.start()  # begin execution
t.join()   # wait for completion
```

### Q7: What is a thread pool? Why use it?
Manages a pool of reusable threads. Avoids overhead of creating/destroying threads.
```python
from concurrent.futures import ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=4) as executor:
    results = executor.map(func, items)
```

### Q8: What is a deadlock?
When two or more threads are waiting for each other's locks, and none can proceed. Fix by acquiring locks in consistent order or using `threading.RLock` (reentrant lock).

### Q9: What is threading.Lock vs threading.RLock?
- `Lock`: simple lock, cannot be acquired again by same thread (reentrant).
- `RLock`: reentrant lock — same thread can acquire multiple times (must release same number of times).

### Q10: What is a condition variable?
Used when threads need to wait for a condition to become true.
```python
cv = threading.Condition()
with cv:
    cv.wait()       # wait for notification
    cv.notify_all()  # wake up waiting threads
```
Real example: Producer-consumer pattern.

### Q11: What is the asyncio event loop?
The core of asyncio. It schedules and runs asynchronous tasks, handles I/O events, and runs callbacks. Use `asyncio.run()` to start.

### Q12: What is `async` and `await`?
- `async def`: defines a coroutine (async function).
- `await`: suspends execution of the coroutine until the awaited task completes.
```python
async def fetch():
    data = await download()
    return data
```

### Q13: What is the difference between `asyncio.gather()` and `asyncio.create_task()`?
- `gather`: runs multiple awaitables concurrently and waits for all.
- `create_task`: schedules a coroutine to run in background, returns Task.

### Q14: What is a semaphore?
Limits access to a resource to a fixed number of threads.
```python
sem = threading.Semaphore(3)  # max 3 threads
with sem:
    access_resource()
```

### Q15: What is an event?
Used for one-time notifications between threads.
```python
event = threading.Event()
# Thread 1: event.set()
# Thread 2: event.wait()  # blocks until set()
```

### Q16: What is the `threading.local()`?
Creates thread-local data — each thread has its own copy.
```python
local_data = threading.local()
local_data.user = "Alice"  # unique to this thread
```
Real example: Storing user session in web apps.

### Q17: What is the Global Interpreter Lock released during?
- I/O operations (file read/write, network, sleep).
- C extension functions (NumPy, Pandas).
- Thread blocking (lock acquire, queue operations).

### Q18: How do you handle exceptions in threads?
Exceptions in threads don't propagate to main thread. Catch inside the thread function.
```python
def worker():
    try:
        risky_operation()
    except Exception as e:
        print(f"Error in thread: {e}")
```

### Q19: What is a Queue in threading?
Thread-safe queue for sharing data between threads.
```python
import queue
q = queue.Queue()
q.put(item)
item = q.get()  # blocks if empty
```
Real example: Producer-consumer in web scraping.

### Q20: When would you use asyncio instead of threading?
Use asyncio when:
- You need very high concurrency (thousands of tasks).
- Tasks are I/O-bound and can be written as coroutines.
- You want cooperative multitasking (tasks decide when to yield).

Use threading when:
- You use blocking libraries that don't support asyncio.
- Tasks involve CPU and I/O mixed work.
- You prefer simpler synchronous code with threads.
