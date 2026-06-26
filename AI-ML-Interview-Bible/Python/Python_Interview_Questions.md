# Python Interview Questions (50 Additional)

## Basic to Intermediate

### Q1: What is the difference between `==` and `is`?
`==` checks value equality. `is` checks identity (same memory location).
```python
a = [1, 2]; b = [1, 2]
print(a == b)  # True
print(a is b)  # False
```

### Q2: How does Python handle variable scope?
LEGB rule: Local, Enclosing, Global, Built-in. Python looks for variables in this order.

### Q3: What is `__name__ == "__main__"`?
Checks if the script is run directly (not imported). Used to prevent code from running on import.
```python
if __name__ == "__main__":
    main()
```

### Q4: Explain `with` statement (context manager).
Manages resources (files, locks) that need setup/cleanup. Uses `__enter__` and `__exit__`.
```python
with open("file.txt") as f:
    content = f.read()  # automatically closes
```

### Q5: What is pickling and unpickling?
Pickling: converting Python object to byte stream. Unpickling: converting byte stream back to object.
```python
import pickle
data = {"key": "value"}
with open("data.pkl", "wb") as f: pickle.dump(data, f)
with open("data.pkl", "rb") as f: data = pickle.load(f)
```
Real example: Saving ML models.

### Q6: What are `*args` and `**kwargs`?
`*args` passes variable positional args (tuple). `**kwargs` passes variable keyword args (dict).
```python
def f(*args, **kwargs):
    print(args, kwargs)
f(1, 2, a=3)  # (1, 2) {'a': 3}
```

### Q7: What is a decorator?
A function that takes another function and extends its behavior. Used for logging, auth, caching.
```python
@timer
def func(): pass
```

### Q8: What is `enumerate()`?
Adds counter to iterable. Used when you need both index and value.
```python
for i, v in enumerate(["a", "b"]):
    print(i, v)  # 0 a, 1 b
```

### Q9: What is the difference between `sort()` and `sorted()`?
`sort()` in-place, returns None. `sorted()` returns new list, original unchanged.

### Q10: What is a lambda? Give use case.
Anonymous one-line function. Used with `map()`, `filter()`, `sorted()`.
```python
sorted(data, key=lambda x: x["age"])
```

### Q11: How do you deep copy an object?
```python
import copy
new_obj = copy.deepcopy(old_obj)  # recursive copy
```

### Q12: What is `zip()`?
Combines iterables element-wise into tuples.
```python
list(zip([1, 2], ["a", "b"]))  # [(1, 'a'), (2, 'b')]
```

### Q13: How do you handle multiple exceptions?
```python
try:
    x = int(input())
except (ValueError, TypeError) as e:
    print(e)
```

### Q14: What is `any()` and `all()`?
`any()`: True if any element is truthy. `all()`: True if all elements are truthy.
```python
any([False, True, False])  # True
all([True, True, False])   # False
```

### Q15: What is `map()` and `filter()`?
`map(func, iterable)`: applies function to every item. `filter(func, iterable)`: keeps items where function returns True.
```python
list(map(str.upper, ["a", "b"]))  # ['A', 'B']
list(filter(lambda x: x > 0, [-1, 0, 5]))  # [5]
```

### Q16: What is `reduce()`?
Applies function cumulatively to items (from `functools`).
```python
from functools import reduce
reduce(lambda a, b: a * b, [1, 2, 3, 4])  # 24
```

### Q17: What is `global` keyword?
Used to modify a global variable inside a function.
```python
count = 0
def increment():
    global count
    count += 1
```

### Q18: What is `nonlocal` keyword?
Used in nested functions to modify a variable in enclosing scope (not global).
```python
def outer():
    x = 0
    def inner():
        nonlocal x
        x += 1
    inner()
    print(x)  # 1
```

### Q19: What is `__slots__`?
Reduces memory by preventing `__dict__` creation. Only declared attributes allowed.
```python
class Point:
    __slots__ = ("x", "y")
```

### Q20: What are type hints? Why use them?
Type hints specify expected types. Improves code readability and enables static type checking.
```python
def add(a: int, b: int) -> int:
    return a + b
```

## Intermediate to Advanced

### Q21: How does `dict.get()` differ from `dict[key]`?
`get(key, default)` returns default if key missing (no KeyError). `dict[key]` raises KeyError.
```python
d = {"a": 1}
print(d.get("b", 0))  # 0 (no error)
```

### Q22: What is `defaultdict`?
A dict that provides default value for missing keys.
```python
from collections import defaultdict
dd = defaultdict(list)
dd["a"].append(1)  # no KeyError
```

### Q23: What is the difference between `remove()`, `pop()`, and `del`?
- `remove(x)`: removes first occurrence of value x.
- `pop(i)`: removes and returns item at index i.
- `del list[i]`: deletes item at index i.

### Q24: How do you handle circular imports?
Restructure code into smaller modules, use lazy imports (import inside function), or use `import` inside `if TYPE_CHECKING` block.

### Q25: What is monkey patching?
Modifying a class or module at runtime (adding/replacing methods). Used in testing (mocking).
```python
class A: def method(self): return "original"
A.method = lambda self: "patched"  # monkey patched
```

### Q26: What is metaprogramming?
Code that manipulates other code. Examples: decorators, metaclasses, `exec()`, `eval()`.

### Q27: What is a metaclass?
A class of a class. `type` is the default metaclass. Used for frameworks (Django models, SQLAlchemy).
```python
class Meta(type):
    def __new__(cls, name, bases, dct):
        dct["added"] = "This was added"
        return super().__new__(cls, name, bases, dct)
```

### Q28: What is the difference between `__new__` and `__init__`?
`__new__` creates and returns a new instance (called first). `__init__` initializes the instance (called second, returns None).

### Q29: What is `functools.lru_cache`?
Caches function results based on arguments. Reduces repeated computation.
```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fib(n):
    return n if n < 2 else fib(n-1) + fib(n-2)
```

### Q30: What is the walrus operator (`:=`)?
Assigns value as part of an expression (Python 3.8+).
```python
if (n := len(data)) > 10:
    print(f"Large: {n}")
```

### Q31: What is `functools.partial`?
Creates a function with some arguments pre-filled.
```python
from functools import partial
def power(base, exp): return base ** exp
square = partial(power, exp=2)
print(square(5))  # 25
```

### Q32: What is `itertools`?
Library with iterator tools: `chain`, `cycle`, `permutations`, `combinations`, `product`.
```python
from itertools import permutations
list(permutations([1, 2, 3], 2))  # [(1,2), (1,3), (2,1), (2,3), (3,1), (3,2)]
```

### Q33: What is a closure?
A function that remembers variables from enclosing scope even after the outer function has finished.
```python
def outer(x):
    def inner(y):
        return x + y
    return inner

add5 = outer(5)
print(add5(3))  # 8
```

### Q34: What is `eval()` and `exec()`?
`eval()` evaluates a single expression and returns result. `exec()` executes arbitrary Python code.
```python
eval("2 + 3")   # 5
exec("x = 5")   # executes, returns None
```
CAUTION: Never use with untrusted input (security risk).

### Q35: What is `sys.argv`?
List of command-line arguments passed to a Python script.
```python
# python script.py arg1 arg2
import sys
print(sys.argv)  # ['script.py', 'arg1', 'arg2']
```

### Q36: What is `os` and `sys` modules?
- `os`: interacts with operating system (files, paths, env variables).
- `sys`: interacts with Python interpreter (argv, path, exit).

### Q37: What is `shutil`?
High-level file operations: copy, move, remove directory trees.
```python
import shutil
shutil.copy("source.txt", "dest.txt")
shutil.rmtree("folder")  # delete directory recursively
```

### Q38: How do you profile Python code?
Use `timeit` for small code, `cProfile` for entire programs.
```python
import cProfile
cProfile.run("my_function()")
```

### Q39: What is unit testing in Python?
Testing individual units of code. Use `unittest` or `pytest`.
```python
import unittest
class TestSum(unittest.TestCase):
    def test_sum(self):
        self.assertEqual(sum([1, 2, 3]), 6)
```

### Q40: What is mocking in tests?
Replacing real objects with fake ones for testing. `unittest.mock` is used to mock external dependencies (APIs, databases).

## Coding Questions

### Q41: Write a function to find the second largest number in a list.
```python
def second_largest(nums):
    unique = list(set(nums))
    if len(unique) < 2: return None
    unique.sort()
    return unique[-2]

print(second_largest([3, 5, 1, 5, 2]))  # 3
```

### Q42: Write a function to check if a string is an anagram.
```python
def is_anagram(s1, s2):
    return sorted(s1) == sorted(s2)

print(is_anagram("listen", "silent"))  # True
```

### Q43: Find all duplicate elements in a list.
```python
def find_duplicates(nums):
    from collections import Counter
    return [num for num, count in Counter(nums).items() if count > 1]

print(find_duplicates([1, 2, 3, 2, 4, 3]))  # [2, 3]
```

### Q44: Implement a Fibonacci generator.
```python
def fib_gen(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

print(list(fib_gen(10)))  # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### Q45: Write a function to reverse a string without using [::-1].
```python
def reverse_string(s):
    chars = list(s)
    left, right = 0, len(chars) - 1
    while left < right:
        chars[left], chars[right] = chars[right], chars[left]
        left += 1
        right -= 1
    return "".join(chars)

print(reverse_string("hello"))  # olleh
```

### Q46: Write a function to find the most frequent element.
```python
def most_frequent(lst):
    from collections import Counter
    return Counter(lst).most_common(1)[0][0]

print(most_frequent([1, 3, 2, 3, 4, 3]))  # 3
```

### Q47: Write a function to merge two sorted lists.
```python
def merge_sorted(l1, l2):
    result = []
    i = j = 0
    while i < len(l1) and j < len(l2):
        if l1[i] < l2[j]:
            result.append(l1[i]); i += 1
        else:
            result.append(l2[j]); j += 1
    result.extend(l1[i:])
    result.extend(l2[j:])
    return result

print(merge_sorted([1, 3, 5], [2, 4, 6]))  # [1, 2, 3, 4, 5, 6]
```

### Q48: Write a function to check balanced parentheses.
```python
def is_balanced(s):
    stack = []
    pairs = {")": "(", "}": "{", "]": "["}
    for ch in s:
        if ch in "({[":
            stack.append(ch)
        elif ch in ")}]":
            if not stack or stack.pop() != pairs[ch]:
                return False
    return len(stack) == 0

print(is_balanced("({[]})"))  # True
print(is_balanced("({[})"))   # False
```

### Q49: Write a function to find the intersection of two lists.
```python
def intersection(l1, l2):
    return list(set(l1) & set(l2))

print(intersection([1, 2, 3, 4], [3, 4, 5, 6]))  # [3, 4]
```

### Q50: Write a function to flatten a nested list (any depth).
```python
def flatten(nested):
    result = []
    for item in nested:
        if isinstance(item, list):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result

print(flatten([1, [2, [3, 4], 5], 6]))  # [1, 2, 3, 4, 5, 6]
```

### Q51: Implement a simple LRU cache.
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache: return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

cache = LRUCache(2)
cache.put(1, "A"); cache.put(2, "B")
print(cache.get(1))  # A
cache.put(3, "C")    # removes key 2
print(cache.get(2))  # -1
```

### Q52: Write a function to find the first non-repeating character.
```python
from collections import Counter

def first_non_repeating(s):
    freq = Counter(s)
    for ch in s:
        if freq[ch] == 1:
            return ch
    return None

print(first_non_repeating("aabbcdd"))  # c
```
