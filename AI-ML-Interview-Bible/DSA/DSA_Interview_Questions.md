# DSA Interview Questions (Medium-Hard)

## Dynamic Programming Basics

### What is DP?
Dynamic Programming solves problems by breaking them into overlapping subproblems, solving each once, and storing results. Two approaches: **memoization** (top-down, recursion + cache) and **tabulation** (bottom-up, iterative table). Used when problem has optimal substructure and overlapping subproblems.

### Fibonacci with Memoization
```python
def fib_memo(n, memo={}):
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib_memo(n-1, memo) + fib_memo(n-2, memo)
    return memo[n]

print(fib_memo(10))  # Output: 55
```
Time: O(n), Space: O(n)

### Fibonacci with Tabulation
```python
def fib_tab(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

print(fib_tab(10))  # Output: 55
```
Time: O(n), Space: O(n), can optimize to O(1)

### 0/1 Knapsack
```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            if weights[i-1] <= w:
                dp[i][w] = max(
                    values[i-1] + dp[i-1][w - weights[i-1]],
                    dp[i-1][w]
                )
            else:
                dp[i][w] = dp[i-1][w]
    return dp[n][capacity]

print(knapsack([1, 2, 3], [6, 10, 12], 5))  # Output: 22
```
Time: O(n*C), Space: O(n*C) (can be O(C) with 1D array)

---

## 40+ Medium-Hard DSA Questions

### 1. Longest Increasing Subsequence (LIS)
*Q:* Find length of longest subsequence that is strictly increasing.  
*A:*
```python
def length_of_lis(nums):
    from bisect import bisect_left
    tails = []
    for num in nums:
        i = bisect_left(tails, num)
        if i == len(tails):
            tails.append(num)
        else:
            tails[i] = num
    return len(tails)
```
Time: O(n log n)

### 2. Longest Common Subsequence (LCS)
*Q:* Length of longest subsequence common to two strings.  
*A:*
```python
def longest_common_subsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n+1) for _ in range(m+1)]
    for i in range(1, m+1):
        for j in range(1, n+1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]
```
Time: O(m*n)

### 3. Coin Change (Minimum Coins)
*Q:* Minimum number of coins to make amount.  
*A:*
```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] = min(dp[i], dp[i - coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
```
Time: O(n*amount)

### 4. Coin Change 2 (Number of Ways)
*Q:* Number of ways to make amount using given coins.  
*A:*
```python
def change(amount, coins):
    dp = [0] * (amount + 1)
    dp[0] = 1
    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] += dp[i - coin]
    return dp[amount]
```

### 5. Edit Distance (Levenshtein Distance)
*Q:* Minimum operations (insert, delete, replace) to convert word1 to word2.  
*A:*
```python
def min_distance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n+1) for _ in range(m+1)]
    for i in range(m+1): dp[i][0] = i
    for j in range(n+1): dp[0][j] = j
    for i in range(1, m+1):
        for j in range(1, n+1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
    return dp[m][n]
```

### 6. House Robber
*Q:* Max money you can rob without robbing adjacent houses.  
*A:*
```python
def rob(nums):
    prev2 = prev1 = 0
    for num in nums:
        curr = max(prev1, prev2 + num)
        prev2 = prev1
        prev1 = curr
    return prev1
```
Time: O(n), Space: O(1)

### 7. House Robber II (Circular)
*Q:* Houses arranged in circle. Can't rob adjacent or first+last.  
*A:*
```python
def rob_circular(nums):
    if len(nums) == 1: return nums[0]
    return max(rob(nums[:-1]), rob(nums[1:]))

def rob(nums):
    prev2 = prev1 = 0
    for num in nums:
        curr = max(prev1, prev2 + num)
        prev2, prev1 = prev1, curr
    return prev1
```

### 8. Jump Game
*Q:* Can you reach last index given max jump length at each position?  
*A:*
```python
def can_jump(nums):
    max_reach = 0
    for i, jump in enumerate(nums):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + jump)
    return True
```

### 9. Jump Game II
*Q:* Minimum jumps to reach last index.  
*A:*
```python
def jump(nums):
    jumps = curr_end = farthest = 0
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        if i == curr_end:
            jumps += 1
            curr_end = farthest
    return jumps
```

### 10. Unique Paths
*Q:* Number of ways to reach bottom-right of m×n grid moving only right/down.  
*A:*
```python
def unique_paths(m, n):
    dp = [[1] * n for _ in range(m)]
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    return dp[m-1][n-1]
```

### 11. Unique Paths II (with Obstacles)
*Q:* Same as above but some cells are obstacles (1 = obstacle).  
*A:*
```python
def unique_paths_with_obstacles(grid):
    if grid[0][0]: return 0
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = 1
    for i in range(m):
        for j in range(n):
            if grid[i][j] or (i == 0 and j == 0): continue
            dp[i][j] = (dp[i-1][j] if i > 0 else 0) + (dp[i][j-1] if j > 0 else 0)
    return dp[m-1][n-1]
```

### 12. Maximum Product Subarray
*Q:* Find contiguous subarray with maximum product.  
*A:*
```python
def max_product(nums):
    max_prod = min_prod = result = nums[0]
    for num in nums[1:]:
        candidates = (num, max_prod * num, min_prod * num)
        max_prod = max(candidates)
        min_prod = min(candidates)
        result = max(result, max_prod)
    return result
```

### 13. Word Break
*Q:* Can string be segmented into dictionary words?  
*A:*
```python
def word_break(s, word_dict):
    words = set(word_dict)
    dp = [False] * (len(s) + 1)
    dp[0] = True
    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in words:
                dp[i] = True
                break
    return dp[-1]
```

### 14. Word Break II (Return All Sentences)
*Q:* Return all possible sentences formed by segmenting string.  
*A:*
```python
def word_break_ii(s, word_dict):
    words = set(word_dict)
    memo = {}
    def dfs(start):
        if start in memo: return memo[start]
        res = []
        if start == len(s):
            return ['']
        for end in range(start + 1, len(s) + 1):
            word = s[start:end]
            if word in words:
                for rest in dfs(end):
                    res.append(word + (' ' + rest if rest else ''))
        memo[start] = res
        return res
    return dfs(0)
```

### 15. Decode Ways
*Q:* Number of ways to decode string ('A'=1 ... 'Z'=26).  
*A:*
```python
def num_decodings(s):
    if not s or s[0] == '0': return 0
    n = len(s)
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    for i in range(2, n + 1):
        if s[i-1] != '0':
            dp[i] += dp[i-1]
        if 10 <= int(s[i-2:i]) <= 26:
            dp[i] += dp[i-2]
    return dp[n]
```

### 16. Longest Palindromic Subsequence
*Q:* Length of longest palindrome subsequence.  
*A:*
```python
def longest_palindrome_subseq(s):
    n = len(s)
    dp = [[0] * n for _ in range(n)]
    for i in range(n-1, -1, -1):
        dp[i][i] = 1
        for j in range(i+1, n):
            if s[i] == s[j]:
                dp[i][j] = dp[i+1][j-1] + 2
            else:
                dp[i][j] = max(dp[i+1][j], dp[i][j-1])
    return dp[0][n-1]
```

### 17. Partition Equal Subset Sum
*Q:* Can array be partitioned into two subsets with equal sum?  
*A:*
```python
def can_partition(nums):
    total = sum(nums)
    if total % 2: return False
    target = total // 2
    dp = [False] * (target + 1)
    dp[0] = True
    for num in nums:
        for i in range(target, num - 1, -1):
            dp[i] = dp[i] or dp[i - num]
    return dp[target]
```

### 18. Longest Valid Parentheses
*Q:* Length of longest valid (well-formed) parentheses substring.  
*A:*
```python
def longest_valid_parentheses(s):
    stack = [-1]
    max_len = 0
    for i, c in enumerate(s):
        if c == '(':
            stack.append(i)
        else:
            stack.pop()
            if not stack:
                stack.append(i)
            else:
                max_len = max(max_len, i - stack[-1])
    return max_len
```

### 19. Trapping Rain Water
*Q:* Water trapped between bars of given heights.  
*A:*
```python
def trap(height):
    l, r = 0, len(height) - 1
    l_max = r_max = water = 0
    while l <= r:
        if height[l] <= height[r]:
            l_max = max(l_max, height[l])
            water += l_max - height[l]
            l += 1
        else:
            r_max = max(r_max, height[r])
            water += r_max - height[r]
            r -= 1
    return water
```

### 20. Merge Intervals
*Q:* Merge overlapping intervals.  
*A:*
```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    res = [intervals[0]]
    for s, e in intervals[1:]:
        if s <= res[-1][1]:
            res[-1][1] = max(res[-1][1], e)
        else:
            res.append([s, e])
    return res
```

### 21. Non-overlapping Intervals
*Q:* Minimum intervals to remove to make rest non-overlapping.  
*A:*
```python
def erase_overlap_intervals(intervals):
    intervals.sort(key=lambda x: x[1])
    count = 0
    end = float('-inf')
    for s, e in intervals:
        if s >= end:
            end = e
        else:
            count += 1
    return count
```

### 22. Meeting Rooms II
*Q:* Minimum number of conference rooms required.  
*A:*
```python
import heapq
def min_meeting_rooms(intervals):
    intervals.sort(key=lambda x: x[0])
    heap = []
    for s, e in intervals:
        if heap and heap[0] <= s:
            heapq.heappop(heap)
        heapq.heappush(heap, e)
    return len(heap)
```

### 23. LRU Cache
*Q:* Design LRU cache with get and put in O(1).  
*A:*
```python
from collections import OrderedDict
class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.cap = capacity
    def get(self, key):
        if key not in self.cache: return -1
        self.cache.move_to_end(key)
        return self.cache[key]
    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.cap:
            self.cache.popitem(last=False)
```

### 24. Alien Dictionary (Topological Sort)
*Q:* Order of letters in alien language given sorted words.  
*A:*
```python
from collections import defaultdict, deque
def alien_order(words):
    graph = defaultdict(set)
    indeg = {c: 0 for w in words for c in w}
    for w1, w2 in zip(words, words[1:]):
        for c1, c2 in zip(w1, w2):
            if c1 != c2:
                if c2 not in graph[c1]:
                    graph[c1].add(c2)
                    indeg[c2] += 1
                break
    q = deque([c for c in indeg if indeg[c] == 0])
    res = []
    while q:
        c = q.popleft()
        res.append(c)
        for nei in graph[c]:
            indeg[nei] -= 1
            if indeg[nei] == 0: q.append(nei)
    return ''.join(res) if len(res) == len(indeg) else ''
```

### 25. Serialize and Deserialize Binary Tree
*Q:* Convert tree to string and back.  
*A:*
```python
from collections import deque
def serialize(root):
    if not root: return ''
    res, q = [], deque([root])
    while q:
        node = q.popleft()
        if node:
            res.append(str(node.val))
            q.append(node.left); q.append(node.right)
        else: res.append('null')
    return ','.join(res)

def deserialize(data):
    if not data: return None
    vals = data.split(',')
    root = TreeNode(int(vals[0]))
    q = deque([root]); i = 1
    while q and i < len(vals):
        node = q.popleft()
        if vals[i] != 'null':
            node.left = TreeNode(int(vals[i])); q.append(node.left)
        i += 1
        if i < len(vals) and vals[i] != 'null':
            node.right = TreeNode(int(vals[i])); q.append(node.right)
        i += 1
    return root
```

### 26. Subsets (Power Set)
*Q:* Return all subsets (powerset).  
*A:*
```python
def subsets(nums):
    res = [[]]
    for num in nums:
        res += [curr + [num] for curr in res]
    return res
```

### 27. Subsets II (with Duplicates)
*Q:* Return all unique subsets.  
*A:*
```python
def subsets_with_dup(nums):
    nums.sort()
    res = [[]]
    for i in range(len(nums)):
        start = 0
        if i > 0 and nums[i] == nums[i-1]:
            start = prev_len
        prev_len = len(res)
        for j in range(start, prev_len):
            res.append(res[j] + [nums[i]])
    return res
```

### 28. Permutations
*Q:* Return all permutations of distinct integers.  
*A:*
```python
def permute(nums):
    res = []
    def backtrack(path, remaining):
        if not remaining:
            res.append(path)
        for i, n in enumerate(remaining):
            backtrack(path + [n], remaining[:i] + remaining[i+1:])
    backtrack([], nums)
    return res
```

### 29. Combination Sum
*Q:* All unique combinations where numbers sum to target (unlimited use).  
*A:*
```python
def combination_sum(candidates, target):
    res = []
    def dfs(i, path, total):
        if total == target:
            res.append(path)
            return
        if i >= len(candidates) or total > target:
            return
        dfs(i, path + [candidates[i]], total + candidates[i])
        dfs(i+1, path, total)
    dfs(0, [], 0)
    return res
```

### 30. Combination Sum II (Each number used once)
*Q:* All unique combos where each candidate used once.  
*A:*
```python
def combination_sum2(candidates, target):
    candidates.sort()
    res = []
    def dfs(i, path, total):
        if total == target: res.append(path); return
        if total > target: return
        for j in range(i, len(candidates)):
            if j > i and candidates[j] == candidates[j-1]: continue
            dfs(j+1, path + [candidates[j]], total + candidates[j])
    dfs(0, [], 0)
    return res
```

### 31. Letter Combinations of Phone Number
*Q:* All letter combos from phone digits (2→"abc").  
*A:*
```python
def letter_combinations(digits):
    if not digits: return []
    mapping = {'2':'abc','3':'def','4':'ghi','5':'jkl',
               '6':'mno','7':'pqrs','8':'tuv','9':'wxyz'}
    res = ['']
    for d in digits:
        res = [p + c for p in res for c in mapping[d]]
    return res
```

### 32. N-Queens
*Q:* All solutions to place n queens on n×n board.  
*A:*
```python
def solve_n_queens(n):
    cols, diag1, diag2 = set(), set(), set()
    res = []
    board = [['.'] * n for _ in range(n)]
    def backtrack(r):
        if r == n:
            res.append([''.join(row) for row in board])
            return
        for c in range(n):
            if c in cols or (r-c) in diag1 or (r+c) in diag2:
                continue
            cols.add(c); diag1.add(r-c); diag2.add(r+c)
            board[r][c] = 'Q'
            backtrack(r+1)
            board[r][c] = '.'
            cols.remove(c); diag1.remove(r-c); diag2.remove(r+c)
    backtrack(0)
    return res
```

### 33. Find Median from Data Stream
*Q:* Design data structure supporting addNum and findMedian in O(log n).  
*A:*
```python
import heapq
class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap (store negatives)
        self.large = []  # min-heap
    def add_num(self, num):
        heapq.heappush(self.small, -num)
        if self.small and self.large and (-self.small[0] > self.large[0]):
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))
    def find_median(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

### 34. Maximum Frequency Stack
*Q:* Design stack that pops most frequent element, tie-broken by recency.  
*A:*
```python
from collections import defaultdict
class FreqStack:
    def __init__(self):
        self.freq = defaultdict(int)
        self.stacks = defaultdict(list)
        self.max_freq = 0
    def push(self, val):
        self.freq[val] += 1
        f = self.freq[val]
        self.stacks[f].append(val)
        self.max_freq = max(self.max_freq, f)
    def pop(self):
        val = self.stacks[self.max_freq].pop()
        self.freq[val] -= 1
        if not self.stacks[self.max_freq]:
            self.max_freq -= 1
        return val
```

### 35. Time Based Key-Value Store
*Q:* Store (key, value, timestamp), retrieve value with largest timestamp ≤ given.  
*A:*
```python
from collections import defaultdict
class TimeMap:
    def __init__(self):
        self.store = defaultdict(list)
    def set(self, key, value, timestamp):
        self.store[key].append((timestamp, value))
    def get(self, key, timestamp):
        values = self.store.get(key, [])
        l, r = 0, len(values) - 1
        res = ''
        while l <= r:
            mid = (l + r) // 2
            if values[mid][0] <= timestamp:
                res = values[mid][1]
                l = mid + 1
            else:
                r = mid - 1
        return res
```

### 36. Top K Frequent Elements
*Q:* Return k most frequent elements.  
*A:*
```python
from collections import Counter
import heapq
def top_k_frequent(nums, k):
    count = Counter(nums)
    return [n for n, _ in heapq.nlargest(k, count.items(), key=lambda x: x[1])]
```

### 37. Task Scheduler
*Q:* Minimum time to execute tasks with cooldown n between same tasks.  
*A:*
```python
from collections import Counter
def least_interval(tasks, n):
    counts = Counter(tasks)
    max_count = max(counts.values())
    num_max = sum(1 for v in counts.values() if v == max_count)
    return max(len(tasks), (max_count - 1) * (n + 1) + num_max)
```

### 38. Longest Consecutive Sequence
*Q:* Length of longest consecutive elements sequence (O(n) time).  
*A:*
```python
def longest_consecutive(nums):
    nums_set = set(nums)
    longest = 0
    for num in nums_set:
        if num - 1 not in nums_set:
            length = 1
            while num + length in nums_set:
                length += 1
            longest = max(longest, length)
    return longest
```

### 39. Rotting Oranges
*Q:* Minutes until all oranges rot (0=empty, 1=fresh, 2=rotten), BFS.  
*A:*
```python
from collections import deque
def oranges_rotting(grid):
    rows, cols = len(grid), len(grid[0])
    q = deque()
    fresh = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2: q.append((r,c,0))
            elif grid[r][c] == 1: fresh += 1
    mins = 0
    while q:
        r, c, t = q.popleft()
        mins = max(mins, t)
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            nr, nc = r+dr, c+dc
            if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                grid[nr][nc] = 2
                fresh -= 1
                q.append((nr, nc, t+1))
    return mins if fresh == 0 else -1
```

### 40. Maximum Width of Binary Tree
*Q:* Maximum width (number of nodes including null) at any level.  
*A:*
```python
from collections import deque
def width_of_binary_tree(root):
    if not root: return 0
    q = deque([(root, 0)])
    max_width = 0
    while q:
        _, first = q[0]
        for _ in range(len(q)):
            node, idx = q.popleft()
            if node.left: q.append((node.left, 2*idx))
            if node.right: q.append((node.right, 2*idx+1))
        max_width = max(max_width, idx - first + 1)
    return max_width
```

### 41. Palindrome Partitioning
*Q:* Partition string such that every substring is palindrome.  
*A:*
```python
def partition(s):
    res = []
    def backtrack(start, path):
        if start == len(s):
            res.append(path)
            return
        for end in range(start, len(s)):
            if s[start:end+1] == s[start:end+1][::-1]:
                backtrack(end+1, path + [s[start:end+1]])
    backtrack(0, [])
    return res
```

### 42. Count Primes (Sieve of Eratosthenes)
*Q:* Count number of primes less than n.  
*A:*
```python
def count_primes(n):
    if n < 2: return 0
    sieve = [True] * n
    sieve[0] = sieve[1] = False
    for i in range(2, int(n**0.5) + 1):
        if sieve[i]:
            for j in range(i*i, n, i):
                sieve[j] = False
    return sum(sieve)
```

### 43. Maximum Points You Can Obtain from Cards
*Q:* Pick k cards from start or end, maximize sum.  
*A:*
```python
def max_score(card_points, k):
    n = len(card_points)
    total = sum(card_points)
    window = n - k
    curr = sum(card_points[:window])
    min_sum = curr
    for i in range(window, n):
        curr += card_points[i] - card_points[i-window]
        min_sum = min(min_sum, curr)
    return total - min_sum if window > 0 else total
```

### 44. Bus Routes
*Q:* Minimum buses to reach target stop.  
*A:*
```python
from collections import defaultdict, deque
def num_buses_to_destination(routes, source, target):
    if source == target: return 0
    stop_to_buses = defaultdict(set)
    for i, stops in enumerate(routes):
        for stop in stops:
            stop_to_buses[stop].add(i)
    q = deque([(source, 0)])
    visited_stops = {source}
    visited_buses = set()
    while q:
        stop, buses = q.popleft()
        for bus in stop_to_buses[stop]:
            if bus in visited_buses: continue
            visited_buses.add(bus)
            for next_stop in routes[bus]:
                if next_stop == target: return buses + 1
                if next_stop not in visited_stops:
                    visited_stops.add(next_stop)
                    q.append((next_stop, buses+1))
    return -1
```

### 45. Sliding Window Median
*Q:* Median of each sliding window of size k.  
*A:*
```python
import heapq
def median_sliding_window(nums, k):
    def add(num, lo, hi):
        if not lo or num <= -lo[0]:
            heapq.heappush(lo, -num)
        else:
            heapq.heappush(hi, num)
        balance(lo, hi)
    def remove(num, lo, hi):
        if num <= -lo[0]:
            lo.remove(-num); heapq.heapify(lo)
        else:
            hi.remove(num); heapq.heapify(hi)
        balance(lo, hi)
    def balance(lo, hi):
        while len(lo) > len(hi) + 1:
            heapq.heappush(hi, -heapq.heappop(lo))
        while len(hi) > len(lo):
            heapq.heappush(lo, -heapq.heappop(hi))
    lo, hi = [], []
    res = []
    for i, num in enumerate(nums):
        add(num, lo, hi)
        if i >= k - 1:
            median = -lo[0] if k % 2 else (-lo[0] + hi[0]) / 2
            res.append(median)
            remove(nums[i-k+1], lo, hi)
    return res
```

---

## Common Patterns Summary

| Pattern | When to Use | Example |
|---------|-------------|---------|
| Two Pointers | Sorted array, find pair | Two Sum (sorted) |
| Sliding Window | Subarray/substring with condition | Max sum subarray of size k |
| BFS | Shortest path, level-order | Number of Islands |
| DFS | Connectivity, all paths | Clone Graph |
| Binary Search | Sorted data, optimization | First/Last occurrence |
| DP: Memoization | Overlapping subproblems | Fibonacci |
| DP: Tabulation | Bottom-up optimization | Knapsack |
| Monotonic Stack | Next greater/smaller | Daily Temperatures |
| Backtracking | All combinations/permutations | N-Queens |
| Heap/Priority Queue | Top k, median, scheduling | Kth Largest Element |
| Union-Find | Connectivity, cycles | Number of Components |
| Topological Sort | Dependency ordering | Course Schedule |
