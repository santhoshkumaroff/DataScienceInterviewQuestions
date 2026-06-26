# Collections

## What is it?
Collections are specialized data structures in Python. Basics: list (ordered, mutable), tuple (ordered, immutable), set (unordered, unique items), dict (key-value pairs). Advanced: deque (double-ended queue), Counter (count hashable items), defaultdict (dict with default factory), OrderedDict (dict with order remembered).

## Why do we use it?
Choosing the right collection affects performance and code clarity. Collections module adds powerful tools for counting, queueing, and grouping data. In AI/ML, you use these daily for data preprocessing and feature engineering.

## Real-Time Example 1
**Log Analysis (Cybersecurity)**

Scenario: A security team analyzes server logs to detect suspicious IPs.

Step 1: Read log file and extract IP addresses.
Step 2: Use `Counter` to count requests per IP.
Step 3: Use `defaultdict` to group requests by hour.
Step 4: Use `deque` to maintain last 100 requests (sliding window).
Step 5: Detect IPs exceeding threshold (DDoS detection).

```python
from collections import Counter, defaultdict, deque

ips = ["192.168.1.1", "10.0.0.1", "192.168.1.1", "10.0.0.2", "192.168.1.1"]
ip_count = Counter(ips)
print(ip_count.most_common(1))  # [('192.168.1.1', 3)]

last_100 = deque(maxlen=100)  # sliding window
```

## Real-Time Example 2
**Inventory Management (Retail)**

Scenario: Walmart manages inventory across stores using Python collections.

Step 1: Use `dict` for product-to-price mapping.
Step 2: Use `defaultdict(list)` to group products by category.
Step 3: Use `set` to find products available in all stores (intersection).
Step 4: Use `tuple` as dictionary key (store_id, product_id).
Step 5: Use `OrderedDict` to maintain product insertion order for display.

```python
from collections import defaultdict

inventory = defaultdict(list)
inventory["Electronics"].append("Laptop")
inventory["Electronics"].append("Phone")
inventory["Clothing"].append("Shirt")
print(dict(inventory))
# {'Electronics': ['Laptop', 'Phone'], 'Clothing': ['Shirt']}
```

## Concept Example 1
**List, Set, Dict Operations**

```python
# List — ordered, duplicates allowed
fruits = ["apple", "banana", "apple", "cherry"]
print(fruits[0])  # apple

# Set — unordered, unique
unique_fruits = set(fruits)
print(unique_fruits)  # {'apple', 'banana', 'cherry'}

# Dict — key-value pairs
prices = {"apple": 100, "banana": 50}
print(prices["apple"])  # 100
```

Input: None.
Output:
```
apple
{'cherry', 'banana', 'apple'}
100
```
Explanation: Lists maintain order and allow duplicates. Sets remove duplicates automatically. Dicts provide fast key-value lookup (O(1)).

## Concept Example 2
**Collections Module**

```python
from collections import Counter, defaultdict, deque, OrderedDict

# Counter
text = "python programming"
freq = Counter(text)
print(freq.most_common(2))  # [('p', 2), ('r', 2)]

# defaultdict
dd = defaultdict(int)
dd["a"] += 1  # no KeyError
print(dd["a"])  # 1

# deque
dq = deque([1, 2, 3])
dq.appendleft(0)
dq.append(4)
print(dq)  # deque([0, 1, 2, 3, 4])

# OrderedDict
od = OrderedDict()
od["a"] = 1; od["b"] = 2
print(od)  # OrderedDict([('a', 1), ('b', 2)])
```

Input: None.
Output:
```
[('p', 2), ('r', 2)]
1
deque([0, 1, 2, 3, 4])
OrderedDict([('a', 1), ('b', 2)])
```
Explanation: Counter counts hashable items. defaultdict provides default value for missing keys. deque is optimized for append/pop from both ends. OrderedDict remembers insertion order.

## Common Mistakes
1. Using list where set is needed (O(n) lookup vs O(1)).
2. Modifying a set/dict while iterating over it (use `list(items)` copy).
3. Forgetting `defaultdict` requires a factory function (like `int`, `list`, `str`).
4. Thinking `OrderedDict` is same as dict (modern dict preserves order too, but OrderedDict has extra methods).
5. Using `Counter` for non-hashable items (lists, dicts as keys).

## How Interviewers Ask This
1. "What is the difference between list and tuple?"
2. "When would you use a set instead of a list?"
3. "What is a defaultdict? Give an example."
4. "How does a Counter work? What is most_common()?"
5. "What is the difference between deque and list?"

## How To Impress The Interviewer
1. Discuss time complexity: list O(n) lookup, set/dict O(1), deque O(1) at ends, list O(n) at beginning.
2. Mention that Python 3.7+ dicts preserve insertion order (like OrderedDict), but OrderedDict has `move_to_end()`.
3. Know that `frozenset` is an immutable set (hashable, can be dict key).
4. Mention `ChainMap` for combining multiple dictionaries without merging.
5. Know `namedtuple` for creating simple classes without boilerplate.

## Most Asked Questions

### Q1: What is the difference between list and tuple?
- List: mutable, `[]`, slower, can append/remove items.
- Tuple: immutable, `()`, faster, cannot modify.
Real example: Tuple for coordinates (`(x, y)`), list for dynamic data.

### Q2: What is the difference between set and frozenset?
- Set: mutable, can add/remove items.
- Frozenset: immutable, hashable (can be used as dict key).
```python
fs = frozenset([1, 2, 3])
d = {fs: "frozen set as key"}  # works
```

### Q3: How does a dictionary work internally?
Dict uses hash table. Keys are hashed, and the hash determines the bucket. Average O(1) lookup. Collisions are handled by open addressing. Real example: Used as cache for ML model features.

### Q4: What is a defaultdict?
A dict that provides a default value for missing keys.
```python
from collections import defaultdict
dd = defaultdict(list)
dd["a"].append(1)  # no KeyError
```
Real example: Grouping emails by domain name.

### Q5: What is a Counter and its common methods?
Counter counts hashable items.
```python
from collections import Counter
c = Counter("aabbcccd")
print(c.most_common(2))  # [('c', 3), ('a', 2)]
c.update("aaa")          # add more counts
print(c["a"])            # 5
```
Real example: Word frequency analysis in NLP.

### Q6: What is a deque and why is it faster than list?
Deque (double-ended queue) is optimized for O(1) append/pop at both ends. List is O(n) for left-side operations.
```python
from collections import deque
dq = deque(maxlen=3)  # fixed-size sliding window
dq.append(1); dq.append(2); dq.append(3)
dq.append(4)  # deque([2, 3, 4]) — 1 is removed
```

### Q7: What is an OrderedDict?
Dict that maintains insertion order (Python 3.7+ dict does too). Extra methods: `move_to_end()`.
```python
from collections import OrderedDict
od = OrderedDict()
od["a"] = 1; od["b"] = 2
od.move_to_end("a")  # moves 'a' to end
```
Real example: Implementing LRU cache.

### Q8: What is namedtuple?
Factory for creating simple classes.
```python
from collections import namedtuple
Point = namedtuple("Point", ["x", "y"])
p = Point(10, 20)
print(p.x, p.y)  # 10 20
```
Real example: Representing database rows.

### Q9: What is ChainMap?
Groups multiple dicts into a single view. Lookups search each dict in order.
```python
from collections import ChainMap
d1 = {"a": 1, "b": 2}
d2 = {"b": 3, "c": 4}
cm = ChainMap(d1, d2)
print(cm["b"])  # 2 (from d1)
```
Real example: Scoped variable lookups (local, global).

### Q10: How do you merge two dictionaries?
```python
# Python 3.9+
merged = d1 | d2
# or
merged = {**d1, **d2}
```

### Q11: What is time complexity of list operations?
- Indexing: O(1), Append: O(1), Pop last: O(1)
- Insert at beginning: O(n), Pop from beginning: O(n)
- Search: O(n), Sort: O(n log n)

### Q12: How do you sort a list of dictionaries by a key?
```python
data = [{"name": "Bob", "age": 30}, {"name": "Alice", "age": 25}]
sorted_data = sorted(data, key=lambda x: x["age"])
```

### Q13: What is the difference between `pop()`, `remove()`, and `del`?
- `pop(i)`: removes and returns item at index i (default last).
- `remove(x)`: removes first occurrence of value x.
- `del list[i]`: deletes item at index.

### Q14: How do you find common elements in two lists?
```python
list1 = [1, 2, 3]; list2 = [2, 3, 4]
common = list(set(list1) & set(list2))  # [2, 3]
```

### Q15: How do you flatten a list of lists?
```python
nested = [[1, 2], [3, 4], [5]]
flat = [item for sublist in nested for item in sublist]
# [1, 2, 3, 4, 5]
```

### Q16: How do you count frequency of items in a list?
```python
items = ["a", "b", "a", "c", "b", "b"]
from collections import Counter
freq = Counter(items)  # {'b': 3, 'a': 2, 'c': 1}
```

### Q17: What is the difference between `copy()` and `deepcopy()` for collections?
- `copy()`: shallow copy — nested objects are referenced.
- `deepcopy()`: recursive copy — everything is independent.

### Q18: How do you create a dictionary with default values?
```python
keys = ["a", "b", "c"]
d = dict.fromkeys(keys, 0)  # {'a': 0, 'b': 0, 'c': 0}
```

### Q19: What is the difference between `list.sort()` and `sorted()`?
- `list.sort()`: in-place, returns None.
- `sorted()`: returns new list, original unchanged.

### Q20: How do you iterate over a dictionary with index?
```python
d = {"a": 1, "b": 2}
for i, (key, value) in enumerate(d.items()):
    print(i, key, value)
```
