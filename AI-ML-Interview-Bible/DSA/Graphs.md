# Graphs

## What is it?
A graph is a collection of vertices (nodes) connected by edges. Graphs can be directed or undirected, weighted or unweighted. Represented as adjacency list (dictionary mapping node → neighbors) or adjacency matrix (2D array). Graphs model any network or relationship.

## Why do we use it?
Graphs are fundamental to AI/ML. Social networks, knowledge graphs (Google Knowledge Graph), recommendation systems (user-item bipartite graphs), neural network architectures (computational graphs in TensorFlow/PyTorch), and molecule structures in drug discovery are all graphs.

## Real-Time Example 1
**Social network friend recommendations:** Users are nodes, friendships are edges. BFS from a user finds friends-of-friends at distance 2. Connected components identify communities. Shortest path (BFS unweighted) finds degrees of separation. PageRank ranks influential users for targeted ads.

## Real-Time Example 2
**Navigation app (Google Maps):** Intersections are nodes, roads are weighted edges (distance/traffic). Dijkstra's algorithm finds shortest path. Topological sort schedules delivery routes with dependencies (warehouse → distribution → customer). Cycle detection prevents infinite loops in routing.

## Concept Example 1
```python
# BFS and DFS traversal
from collections import deque

def bfs(graph, start):
    visited = set()
    q = deque([start])
    visited.add(start)
    result = []
    while q:
        node = q.popleft()
        result.append(node)
        for nei in graph[node]:
            if nei not in visited:
                visited.add(nei)
                q.append(nei)
    return result

def dfs(graph, start):
    visited = set()
    result = []
    def explore(node):
        visited.add(node)
        result.append(node)
        for nei in graph[node]:
            if nei not in visited:
                explore(nei)
    explore(start)
    return result

graph = {0: [1, 2], 1: [2], 2: [0, 3], 3: [3]}
print(bfs(graph, 0))  # Output: [0, 1, 2, 3]
print(dfs(graph, 0))  # Output: [0, 1, 2, 3]
```
**Input:** Adjacency list graph, start = 0
**Output:** BFS = [0,1,2,3], DFS = [0,1,2,3]
**Explanation:** BFS uses queue (level by level). DFS uses stack/recursion (deep first). Both O(V+E) time.

## Concept Example 2
```python
# Cycle detection in directed graph using DFS
def has_cycle(graph):
    WHITE, GRAY, BLACK = 0, 1, 2
    color = {node: WHITE for node in graph}

    def dfs(node):
        color[node] = GRAY
        for nei in graph[node]:
            if color[nei] == GRAY:
                return True
            if color[nei] == WHITE and dfs(nei):
                return True
        color[node] = BLACK
        return False

    for node in graph:
        if color[node] == WHITE and dfs(node):
            return True
    return False

graph = {0: [1], 1: [2], 2: [0]}  # cycle: 0→1→2→0
print(has_cycle(graph))  # Output: True
```
**Input:** Graph with cycle 0→1→2→0
**Output:** True
**Explanation:** Three colors: WHITE (unvisited), GRAY (in current path), BLACK (done). If we re-enter GRAY node, cycle exists.

## Common Mistakes
- Forgetting visited set in BFS/DFS (infinite loops)
- Confusing directed vs undirected cycle detection
- Not considering disconnected graphs (iterate all nodes)
- Using adjacency matrix (O(V^2) space) when list (O(V+E)) suffices

## How Interviewers Ask This
- "Clone a graph"
- "Find if path exists between two nodes"
- "Course schedule (topological sort / cycle detection)"
- "Number of connected components"

## How To Impress The Interviewer
- Know BFS for shortest path in unweighted, DFS for connectivity
- Use 3-color DFS for cycle detection in directed graphs
- Master topological sort (Kahn's algorithm + DFS-based)
- For AI/ML: computational graphs (TensorBoard), knowledge graphs (Neo4j), graph neural networks (GNNs), PageRank

## Most Asked Questions

**1. Clone Graph**  
*Q:* Deep copy of undirected graph (each node has neighbors list).  
*A:*
```python
def clone_graph(node):
    if not node: return None
    clones = {}
    def dfs(n):
        if n in clones:
            return clones[n]
        clone = Node(n.val)
        clones[n] = clone
        for nei in n.neighbors:
            clone.neighbors.append(dfs(nei))
        return clone
    return dfs(node)
```

**2. Course Schedule (Cycle Detection / Topological Sort)**  
*Q:* Can you finish all courses given prerequisites?  
*A:*
```python
from collections import defaultdict, deque
def can_finish(num_courses, prerequisites):
    graph = defaultdict(list)
    indegree = [0] * num_courses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        indegree[course] += 1
    q = deque([i for i in range(num_courses) if indegree[i] == 0])
    count = 0
    while q:
        node = q.popleft()
        count += 1
        for nei in graph[node]:
            indegree[nei] -= 1
            if indegree[nei] == 0:
                q.append(nei)
    return count == num_courses
```

**3. Number of Islands**  
*Q:* Count connected components of '1's in binary matrix.  
*A:*
```python
def num_islands(grid):
    if not grid: return 0
    rows, cols = len(grid), len(grid[0])
    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                stack = [(r,c)]
                while stack:
                    cr, cc = stack.pop()
                    if 0 <= cr < rows and 0 <= cc < cols and grid[cr][cc] == '1':
                        grid[cr][cc] = '0'
                        stack.extend([(cr+1,cc),(cr-1,cc),(cr,cc+1),(cr,cc-1)])
    return count
```

**4. Pacific Atlantic Water Flow**  
*Q:* Cells where water can flow to both Pacific and Atlantic oceans.  
*A:*
```python
def pacific_atlantic(heights):
    if not heights: return []
    R, C = len(heights), len(heights[0])
    pac, atl = set(), set()
    def dfs(r, c, visited):
        visited.add((r,c))
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            nr, nc = r+dr, c+dc
            if (0 <= nr < R and 0 <= nc < C and
                (nr,nc) not in visited and
                heights[nr][nc] >= heights[r][c]):
                dfs(nr, nc, visited)
    for r in range(R):
        dfs(r, 0, pac)
        dfs(r, C-1, atl)
    for c in range(C):
        dfs(0, c, pac)
        dfs(R-1, c, atl)
    return list(pac & atl)
```

**5. Word Ladder**  
*Q:* Shortest transformation sequence from beginWord to endWord (one letter change).  
*A:*
```python
from collections import deque, defaultdict
def ladder_length(begin, end, word_list):
    words = set(word_list)
    if end not in words: return 0
    q = deque([(begin, 1)])
    visited = {begin}
    while q:
        word, dist = q.popleft()
        if word == end: return dist
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                nw = word[:i] + c + word[i+1:]
                if nw in words and nw not in visited:
                    visited.add(nw)
                    q.append((nw, dist+1))
    return 0
```

**6. Shortest Path in Grid with Obstacles (BFS)**  
*Q:* Shortest path from (0,0) to (R-1,C-1) with up to k obstacle eliminations.  
*A:*
```python
from collections import deque
def shortest_path(grid, k):
    R, C = len(grid), len(grid[0])
    q = deque([(0,0,0,k)])
    visited = {(0,0,k)}
    while q:
        r, c, d, elim = q.popleft()
        if r == R-1 and c == C-1: return d
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            nr, nc = r+dr, c+dc
            if 0 <= nr < R and 0 <= nc < C:
                new_elim = elim - grid[nr][nc]
                if new_elim >= 0 and (nr,nc,new_elim) not in visited:
                    visited.add((nr,nc,new_elim))
                    q.append((nr,nc,d+1,new_elim))
    return -1
```

**7. Alien Dictionary**  
*Q:* Order of letters from alien language given sorted words.  
*A:*
```python
from collections import defaultdict, deque
def alien_order(words):
    graph = defaultdict(set)
    indegree = {c: 0 for w in words for c in w}
    for w1, w2 in zip(words, words[1:]):
        for c1, c2 in zip(w1, w2):
            if c1 != c2:
                if c2 not in graph[c1]:
                    graph[c1].add(c2)
                    indegree[c2] += 1
                break
    q = deque([c for c in indegree if indegree[c] == 0])
    res = []
    while q:
        c = q.popleft()
        res.append(c)
        for nei in graph[c]:
            indegree[nei] -= 1
            if indegree[nei] == 0:
                q.append(nei)
    return ''.join(res) if len(res) == len(indegree) else ''
```

**8. Graph Valid Tree**  
*Q:* Given n nodes and edges, check if graph is a valid tree.  
*A:*
```python
def valid_tree(n, edges):
    if len(edges) != n - 1: return False
    parent = list(range(n))
    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    def union(x, y):
        rx, ry = find(x), find(y)
        if rx == ry: return False
        parent[ry] = rx
        return True
    for u, v in edges:
        if not union(u, v): return False
    return True
```

**9. Network Delay Time (Dijkstra)**  
*Q:* Time for signal to reach all nodes from K (shortest path in weighted graph).  
*A:*
```python
import heapq
def network_delay_time(times, n, k):
    graph = [[] for _ in range(n+1)]
    for u, v, w in times:
        graph[u].append((v, w))
    dist = [float('inf')] * (n+1)
    dist[k] = 0
    heap = [(0, k)]
    while heap:
        d, node = heapq.heappop(heap)
        if d > dist[node]: continue
        for nei, w in graph[node]:
            if d + w < dist[nei]:
                dist[nei] = d + w
                heapq.heappush(heap, (dist[nei], nei))
    max_dist = max(dist[1:])
    return max_dist if max_dist != float('inf') else -1
```

**10. Number of Connected Components in Undirected Graph**  
*Q:* Count connected components using Union-Find.  
*A:*
```python
def count_components(n, edges):
    parent = list(range(n))
    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    def union(x, y):
        rx, ry = find(x), find(y)
        if rx != ry:
            parent[ry] = rx
            return True
        return False
    for u, v in edges:
        if union(u, v): n -= 1
    return n
```
