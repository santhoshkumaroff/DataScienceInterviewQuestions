# Python Basics

## What is it?
Python basics include variables (containers for data), data types (int, float, string, bool, list, tuple, dict, set), control flow (if/else, loops), functions (reusable code blocks), list comprehensions (shortcut to create lists), and lambda (anonymous one-line functions).

## Why do we use it?
Python is the most popular language for AI/ML because it is simple, readable, and has powerful libraries. Understanding basics is mandatory for every interview. 90% of Python interview questions test fundamentals.

## Real-Time Example 1
**E-commerce Discount Calculator (Amazon)**

Scenario: Amazon needs to calculate discounts on products during a sale.

Step 1: Get product price and discount percentage as input.
Step 2: Use if/elif to decide discount tier (10% for <1000, 20% for 1000-5000, 30% for >5000).
Step 3: Calculate final price using formula.
Step 4: Return discounted price using a function.
Step 5: Use lambda to sort products by discount amount.

```python
def calculate_discount(price, tier):
    if price < 1000:
        return price * 0.9
    elif price <= 5000:
        return price * 0.8
    else:
        return price * 0.7

products = [1200, 300, 6000]
discounted = list(map(lambda p: calculate_discount(p, 1), products))
print(discounted)  # [1080.0, 270.0, 4200.0]
```

## Real-Time Example 2
**Student Grade Analyzer (Education)**

Scenario: A school wants to analyze student grades and classify them.

Step 1: Store student names and marks in a dictionary.
Step 2: Use list comprehension to filter students who passed (>40).
Step 3: Use lambda with sorted() to rank students by marks.
Step 4: Use control flow to assign grades (A/B/C/Fail).
Step 5: Generate a pass/fail report using a function.

```python
students = {"Alice": 85, "Bob": 39, "Charlie": 72, "Diana": 55}
passed = {name: marks for name, marks in students.items() if marks >= 40}
ranked = sorted(students.items(), key=lambda x: x[1], reverse=True)
print(passed)   # {'Alice': 85, 'Charlie': 72, 'Diana': 55}
print(ranked)   # [('Alice', 85), ('Charlie', 72), ('Diana', 55), ('Bob', 39)]
```

## Concept Example 1
**Variables and Data Types**

```python
name = "John"        # str
age = 25             # int
height = 5.9         # float
is_student = True    # bool

print(type(name), type(age), type(height), type(is_student))
```

Input: None (hardcoded values).
Output: `<class 'str'> <class 'int'> <class 'float'> <class 'bool'>`
Explanation: Python automatically assigns data types based on the value. You don't need to declare types.

## Concept Example 2
**List Comprehension and Lambda**

```python
# List comprehension: square of even numbers
squares = [x**2 for x in range(10) if x % 2 == 0]
print(squares)

# Lambda with filter
nums = [1, 2, 3, 4, 5, 6]
evens = list(filter(lambda x: x % 2 == 0, nums))
print(evens)
```

Input: None.
Output:
```
[0, 4, 16, 36, 64]
[2, 4, 6]
```
Explanation: List comprehension creates a new list by applying expression `x**2` only when `x % 2 == 0`. Lambda creates an anonymous function that checks for even numbers.

## Common Mistakes
1. Confusing `==` (comparison) with `=` (assignment).
2. Forgetting colon `:` after if, for, while, def statements.
3. Modifying a list while iterating over it (causes skip or error).
4. Using mutable default arguments in functions (`def f(x=[])` causes shared state).
5. Thinking `is` and `==` are same — `is` checks identity, `==` checks value.

## How Interviewers Ask This
1. "What is the difference between a list and a tuple?"
2. "Write a function to check if a string is a palindrome."
3. "Explain list comprehension with an example."
4. "What does the `pass` keyword do?"
5. "What is the difference between `deepcopy` and `shallow copy`?"

## How To Impress The Interviewer
1. Mention that Python uses dynamic typing but you can use type hints (`def add(a: int, b: int) -> int:`) for production code.
2. Explain that `list` is implemented as a dynamic array internally, and `tuple` is immutable because it is cached for performance.
3. Know that list comprehensions are faster than for-loops because they avoid `append()` overhead.
4. Understand variable scopes: LEGB rule (Local, Enclosing, Global, Built-in).
5. Show awareness of `_` as a throwaway variable name convention.

## Most Asked Questions

### Q1: What is the difference between list and tuple?
- List: mutable, slower, uses square brackets `[]`, can modify after creation.
- Tuple: immutable, faster, uses parentheses `()`, cannot modify after creation.
- Real example: Use tuple for fixed data like days of week; use list for dynamic data like user names.

### Q2: What is a list comprehension?
A concise way to create lists.
```python
squares = [x**2 for x in range(10) if x % 2 == 0]
```
Real example: Filter all even numbers from a dataset before ML training.

### Q3: What is a lambda function?
An anonymous one-line function.
```python
add = lambda a, b: a + b
print(add(3, 5))  # 8
```
Real example: Used in Pandas `df.apply(lambda x: x*2)` for data transformation.

### Q4: Explain mutable vs immutable data types.
- Mutable: list, dict, set, bytearray — can change after creation.
- Immutable: int, float, str, tuple, frozenset, bytes — cannot change.
- Real example: Use tuple for dictionary keys (only immutable types work as keys).

### Q5: What are *args and **kwargs?
- `*args` passes variable number of positional arguments as tuple.
- `**kwargs` passes variable number of keyword arguments as dict.
```python
def func(*args, **kwargs):
    print(args, kwargs)
func(1, 2, a=3, b=4)  # (1, 2) {'a': 3, 'b': 4}
```
Real example: Wrapper functions in Flask/Django decorators.

### Q6: What is pass, continue, break?
- `pass`: do nothing (placeholder).
- `continue`: skip current iteration, go to next.
- `break`: exit the loop completely.
```python
for i in range(5):
    if i == 2: pass
    if i == 3: continue
    if i == 4: break
    print(i)  # 0 1
```

### Q7: Explain shallow copy vs deep copy.
- Shallow copy: copies object but nested objects are referenced (changes affect both).
- Deep copy: recursively copies everything (independent).
```python
import copy
a = [1, [2, 3]]
b = copy.copy(a)      # shallow
c = copy.deepcopy(a)  # deep
```
Real example: Duplicating ML model config dict without modifying original.

### Q8: What is type() and isinstance()?
- `type(obj)` returns exact type.
- `isinstance(obj, class)` checks if object is instance of class (supports inheritance).
```python
isinstance("hello", str)  # True
isinstance([], list)      # True
```
Real example: Validate user input type before processing.

### Q9: What is the difference between `==` and `is`?
- `==` checks value equality.
- `is` checks identity (same memory location).
```python
a = [1, 2]; b = [1, 2]
print(a == b)  # True
print(a is b)  # False
```

### Q10: How does Python manage memory?
Python uses reference counting and garbage collection. Objects are deleted when reference count reaches 0. The `gc` module handles cyclic references. Real example: Large ML models are deleted using `del model` followed by `gc.collect()` to free memory.

### Q11: Write a function to find the factorial of a number.
```python
def factorial(n):
    return 1 if n <= 1 else n * factorial(n - 1)

print(factorial(5))  # 120
```

### Q12: Write a function to check palindrome.
```python
def is_palindrome(s):
    return s == s[::-1]

print(is_palindrome("racecar"))  # True
```

### Q13: What is the difference between `input()` and `raw_input()`?
In Python 2, `raw_input()` returns string, `input()` evaluates as code. In Python 3, only `input()` exists (always returns string).

### Q14: How do you swap two variables in Python?
```python
a, b = b, a  # tuple unpacking
```

### Q15: What is the ternary operator in Python?
```python
x = 10
result = "Even" if x % 2 == 0 else "Odd"
print(result)  # Even
```

### Q16: Explain the `range()` function.
```python
range(start, stop, step)
list(range(1, 10, 2))  # [1, 3, 5, 7, 9]
```
Real example: Iterating over indices in a for loop.

### Q17: What is `enumerate()`?
Adds a counter to an iterable.
```python
for i, val in enumerate(["a", "b", "c"], start=1):
    print(i, val)  # 1 a, 2 b, 3 c
```

### Q18: What is `zip()`?
Combines multiple iterables element-wise.
```python
names = ["Alice", "Bob"]
scores = [85, 90]
print(list(zip(names, scores)))  # [('Alice', 85), ('Bob', 90)]
```

### Q19: What is the difference between `sort()` and `sorted()`?
- `sort()` modifies list in-place, returns None.
- `sorted()` returns new sorted list, original unchanged.
```python
a = [3, 1, 2]
b = sorted(a)  # b = [1,2,3], a = [3,1,2]
a.sort()       # a = [1,2,3]
```

### Q20: How do you remove duplicates from a list?
```python
list(set([1, 2, 2, 3, 3]))  # [1, 2, 3]  (order not preserved)
```
Or preserve order: `list(dict.fromkeys([1, 2, 2, 3]))`.
