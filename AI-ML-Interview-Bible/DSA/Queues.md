# Queues

## What is it?
A queue is a First-In-First-Out (FIFO) data structure. Elements are added at the rear (enqueue) and removed from the front (dequeue). Python's collections.deque provides O(1) operations for both ends. Queues represent any waiting line where order matters.

## Why do we use it?
Queues are essential for BFS in graph/tree traversal, task scheduling in distributed ML systems, managing data pipelines, and streaming data processing. In production ML, queues buffer training data, manage inference requests, and handle job scheduling in Kubernetes/Airflow.

## Real-Time Example 1
**ML inference serving system:** Requests from users arrive and are queued. The model server processes them in FIFO order. Priority queue handles premium users first. Circular queue efficiently reuses buffer space. Deque allows batching recent requests together for optimized GPU inference.

## Real-Time Example 2
**Real-time fraud detection pipeline:** Transaction events are pushed into a queue. A BFS-based graph analyzer checks connections between accounts. Priority queue scores transactions by risk level. Circular queue stores last 1000 transactions for sliding window feature computation.

## Concept Example 1
```python
# Circular queue implementation
class CircularQueue:
    def __init__(self, k):
        self.queue = [None] * k
        self.head = self.tail = self.size = 0
        self.capacity = k

    def enqueue(self, value):
        if self.is_full():
            return False
        self.queue[self.tail] = value
        self.tail = (self.tail + 1) % self.capacity
        self.size += 1
        return True

    def dequeue(self):
        if self.is_empty():
            return False
        self.head = (self.head + 1) % self.capacity
        self.size -= 1
        return True

    def front(self):
        return -1 if self.is_empty() else self.queue[self.head]

    def is_empty(self):
        return self.size == 0

    def is_full(self):
        return self.size == self.capacity

cq = CircularQueue(3)
cq.enqueue(1); cq.enqueue(2); cq.enqueue(3)
print(cq.enqueue(4))  # Output: False (full)
print(cq.front())     # Output: 1
```
**Input:** Queue of capacity 3, push 1,2,3, then try 4
**Output:** False (full), front = 1
**Explanation:** Circular reuse of array. Head/tail wrap around using modulo. O(1) all operations.

## Concept Example 2
```python
from collections import deque

# BFS using queue for graph traversal
def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    result = []
    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return result

graph = {0: [1, 2], 1: [2], 2: [0, 3], 3: [3]}
print(bfs(graph, 2))  # Output: [2, 0, 3, 1]
```
**Input:** Graph adjacency list, start node 2
**Output:** [2, 0, 3, 1]
**Explanation:** BFS explores level by level using queue. Visited set prevents cycles. O(V+E) time.

## Common Mistakes
- Confusing stack (LIFO) with queue (FIFO) in BFS vs DFS
- Forgetting to mark visited nodes before enqueueing (infinite loop)
- Not handling empty queue (deque from empty raises error)
- Using list as queue (pop(0) is O(n), use deque for O(1))

## How Interviewers Ask This
- "Implement queue using stacks"
- "Design a circular deque"
- "Generate binary numbers from 1 to n using queue"
- "Find shortest path in binary matrix using BFS"

## How To Impress The Interviewer
- Always use collections.deque, not list, for queue operations
- Know BFS template: visited set, queue initialization, loop, process neighbors
- Understand priority queue (heapq) for Dijkstra, A*, and task scheduling
- For AI/ML: queues buffer training batches, manage streaming features, schedule model retraining

## Most Asked Questions

**1. Implement Queue Using Stacks**  
*Q:* Use two stacks to implement FIFO queue (with all operations O(1) amortized).  
*A:*
```python
class MyQueue:
    def __init__(self):
        self.input, self.output = [], []
    def push(self, x):
        self.input.append(x)
    def pop(self):
        self.peek()
        return self.output.pop()
    def peek(self):
        if not self.output:
            while self.input:
                self.output.append(self.input.pop())
        return self.output[-1]
    def empty(self):
        return not self.input and not self.output
```

**2. Generate Binary Numbers**  
*Q:* Generate first n binary numbers using queue.  
*A:*
```python
from collections import deque
def generate_binary(n):
    res = []
    q = deque(['1'])
    for _ in range(n):
        curr = q.popleft()
        res.append(curr)
        q.append(curr + '0')
        q.append(curr + '1')
    return res
```

**3. Priority Queue / Kth Largest Element**  
*Q:* Find kth largest element using min-heap (priority queue).  
*A:*
```python
import heapq
def find_kth_largest(nums, k):
    heap = nums[:k]
    heapq.heapify(heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    return heap[0]
```

**4. Design Circular Deque**  
*Q:* Design deque with insertFront, insertLast, deleteFront, deleteLast, getFront, getRear.  
*A:*
```python
class MyCircularDeque:
    def __init__(self, k):
        self.deque = [None] * k
        self.front = self.rear = self.size = 0
        self.cap = k
    def insert_front(self, value):
        if self.is_full(): return False
        self.front = (self.front - 1) % self.cap
        self.deque[self.front] = value
        self.size += 1; return True
    def insert_last(self, value):
        if self.is_full(): return False
        self.deque[self.rear] = value
        self.rear = (self.rear + 1) % self.cap
        self.size += 1; return True
    def delete_front(self):
        if self.is_empty(): return False
        self.front = (self.front + 1) % self.cap
        self.size -= 1; return True
    def delete_last(self):
        if self.is_empty(): return False
        self.rear = (self.rear - 1) % self.cap
        self.size -= 1; return True
    def get_front(self):
        return -1 if self.is_empty() else self.deque[self.front]
    def get_rear(self):
        return -1 if self.is_empty() else self.deque[(self.rear - 1) % self.cap]
    def is_empty(self): return self.size == 0
    def is_full(self): return self.size == self.cap
```

**5. Right View of Binary Tree**  
*Q:* Return values of nodes visible from right side (BFS level order).  
*A:*
```python
from collections import deque
def right_side_view(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        for i in range(len(q)):
            node = q.popleft()
            if i == 0: res.append(node.val)
            if node.right: q.append(node.right)
            if node.left: q.append(node.left)
    return res
```

**6. Minimum Knight Moves**  
*Q:* Minimum moves for knight from (0,0) to (x,y) on infinite board.  
*A:*
```python
from collections import deque
def min_knight_moves(x, y):
    dirs = [(2,1),(1,2),(-1,2),(-2,1),(-2,-1),(-1,-2),(1,-2),(2,-1)]
    q = deque([(0,0,0)])
    visited = set([(0,0)])
    while q:
        cx, cy, d = q.popleft()
        if cx == x and cy == y: return d
        for dx, dy in dirs:
            nx, ny = cx+dx, cy+dy
            if (nx, ny) not in visited and abs(nx) + abs(ny) <= 300:
                visited.add((nx, ny))
                q.append((nx, ny, d+1))
    return -1
```

**7. Task Scheduler**  
*Q:* Minimum time to execute tasks with cooldown n between same tasks.  
*A:*
```python
from collections import Counter, deque
def least_interval(tasks, n):
    counts = Counter(tasks)
    max_freq = max(counts.values())
    max_count = sum(1 for v in counts.values() if v == max_freq)
    return max(len(tasks), (max_freq - 1) * (n + 1) + max_count)
```

**8. Sliding Window Maximum**  
*Q:* Maximum in each sliding window of size k using deque.  
*A:*
```python
from collections import deque
def max_sliding_window(nums, k):
    q, res = deque(), []
    for i, n in enumerate(nums):
        while q and nums[q[-1]] < n:
            q.pop()
        q.append(i)
        if q[0] == i - k:
            q.popleft()
        if i >= k - 1:
            res.append(nums[q[0]])
    return res
```

**9. Number of Islands (BFS)**  
*Q:* Count number of islands (1 = land, 0 = water) in 2D grid.  
*A:*
```python
from collections import deque
def num_islands(grid):
    if not grid: return 0
    rows, cols = len(grid), len(grid[0])
    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                q = deque([(r,c)])
                while q:
                    cr, cc = q.popleft()
                    for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
                        nr, nc = cr+dr, cc+dc
                        if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == '1':
                            grid[nr][nc] = '0'
                            q.append((nr, nc))
    return count
```

**10. Shortest Path in Binary Matrix**  
*Q:* Shortest path from (0,0) to (n-1,n-1) in binary matrix (8-directional).  
*A:*
```python
from collections import deque
def shortest_path(grid):
    n = len(grid)
    if grid[0][0] or grid[n-1][n-1]:
        return -1
    q = deque([(0,0,1)])
    grid[0][0] = 1
    dirs = [(-1,-1),(-1,0),(-1,1),(0,-1),(0,1),(1,-1),(1,0),(1,1)]
    while q:
        r, c, d = q.popleft()
        if r == n-1 and c == n-1: return d
        for dr, dc in dirs:
            nr, nc = r+dr, c+dc
            if 0 <= nr < n and 0 <= nc < n and not grid[nr][nc]:
                grid[nr][nc] = 1
                q.append((nr, nc, d+1))
    return -1
```
