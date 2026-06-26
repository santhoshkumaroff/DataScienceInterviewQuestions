# Decorators

## What is it?
A decorator is a function that takes another function and extends its behavior without modifying it. Written with `@decorator` syntax. Built-in decorators: `@staticmethod` (method doesn't need self), `@classmethod` (method receives class, not instance), `@property` (access method like attribute). Class decorators modify class behavior.

## Why do we use it?
Decorators follow the DRY (Don't Repeat Yourself) principle. Used in production for logging, authentication, timing, caching, and access control. Flask/Django use decorators for routes (`@app.route()`). In AI/ML, decorators cache expensive computations and time model training.

## Real-Time Example 1
**API Rate Limiting (Backend)**

Scenario: A REST API needs to limit users to 100 requests per minute.

Step 1: Create `rate_limit` decorator that stores request timestamps.
Step 2: Decorate API endpoint functions with `@rate_limit`.
Step 3: On each call, check if user exceeded limit.
Step 4: If exceeded, return "429 Too Many Requests".
Step 5: If within limit, execute the actual function.

```python
import time

def rate_limit(max_calls, period=60):
    def decorator(func):
        calls = []
        def wrapper(*args, **kwargs):
            now = time.time()
            calls[:] = [c for c in calls if now - c < period]
            if len(calls) >= max_calls:
                return "429 Too Many Requests"
            calls.append(now)
            return func(*args, **kwargs)
        return wrapper
    return decorator

@rate_limit(max_calls=5, period=10)
def get_data():
    return "Data fetched"
```

## Real-Time Example 2
**ML Model Caching (Data Science)**

Scenario: An ML pipeline recomputes the same features repeatedly. Cache results to save time.

Step 1: Create `cache_result` decorator using `functools.lru_cache`.
Step 2: Decorate expensive feature engineering function.
Step 3: First call computes and caches.
Step 4: Subsequent calls with same args return cached result.
Step 5: Set maxsize to limit memory usage.

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def compute_features(text):
    print(f"Computing for: {text}")
    return len(text)  # simplified

print(compute_features("hello"))   # Computes
print(compute_features("hello"))   # Uses cache
print(compute_features("world"))   # Computes
```

## Concept Example 1
**Basic Function Decorator**

```python
def timer(func):
    def wrapper(*args, **kwargs):
        import time
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end-start:.2f}s")
        return result
    return wrapper

@timer
def slow_function():
    sum(range(1000000))
    return "Done"

print(slow_function())
```

Input: None.
Output:
```
slow_function took 0.02s
Done
```
Explanation: `@timer` is equivalent to `slow_function = timer(slow_function)`. The decorator wraps the original function with timing logic without changing its code.

## Concept Example 2
**Built-in Decorators**

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def area(self):
        return 3.14 * self._radius ** 2

    @classmethod
    def from_diameter(cls, diameter):
        return cls(diameter / 2)

    @staticmethod
    def description():
        return "Circle shape"

c = Circle(5)
print(c.area)              # 78.5 (accessed like attribute)
c2 = Circle.from_diameter(10)  # classmethod
print(Circle.description())    # Circle shape
```

Input: None.
Output:
```
78.5
Circle shape
```
Explanation: `@property` makes `area()` accessible as `c.area` (no parentheses). `@classmethod` creates alternate constructor. `@staticmethod` is a utility function that doesn't need self or cls.

## Common Mistakes
1. Forgetting to return `wrapper` from decorator (only works once).
2. Not using `functools.wraps` — loses original function name and docstring.
3. Decorating a function that takes arguments without using `*args, **kwargs`.
4. Applying multiple decorators in wrong order (applied bottom-up).
5. Using `@property` with private attribute naming incorrectly.

## How Interviewers Ask This
1. "What is a decorator? How does it work?"
2. "Write a decorator that measures execution time."
3. "What is the difference between @staticmethod and @classmethod?"
4. "What does @property do? Give an example."
5. "How do you pass arguments to a decorator?"

## How To Impress The Interviewer
1. Use `functools.wraps` to preserve metadata (name, docstring) of decorated function.
2. Explain that decorators run at import time (function definition), not at call time.
3. Know how to create a decorator with and without arguments using nested functions.
4. Mention class-based decorators using `__call__` magic method.
5. Explain that `@property` can also have setters (`@property_name.setter`) and deleters.

## Most Asked Questions

### Q1: What is a decorator in Python?
A decorator is a function that takes another function as argument and extends its behavior.
```python
@decorator
def func(): pass  # same as func = decorator(func)
```

### Q2: How do you write a decorator that logs function calls?
```python
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper
```

### Q3: What is functools.wraps and why use it?
`functools.wraps` copies the original function's name, docstring, and metadata to the wrapper.
```python
from functools import wraps
def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```
Without it, `func.__name__` becomes "wrapper" instead of the original name.

### Q4: What is the difference between @staticmethod and @classmethod?
- `@staticmethod`: no self or cls, just a regular function inside class.
- `@classmethod`: receives cls as first param, can access/modify class state.
```python
class MyClass:
    @staticmethod
    def add(x, y): return x + y

    @classmethod
    def create(cls): return cls()
```

### Q5: What does @property do?
Makes a method accessible as an attribute. Often used with getter/setter pattern.
```python
class Person:
    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Invalid age")
        self._age = value
```

### Q6: Can decorators take arguments?
Yes, using nested functions (three levels).
```python
def repeat(n):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(n):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def say_hi():
    print("Hi")
```

### Q7: What happens when you apply multiple decorators?
Applied bottom-up (nearest to function first).
```python
@decorator1
@decorator2
def func(): pass
# Equivalent to: func = decorator1(decorator2(func))
```

### Q8: What is a class decorator?
A decorator that modifies or registers a class.
```python
def add_repr(cls):
    cls.__repr__ = lambda self: f"{cls.__name__}()"
    return cls

@add_repr
class MyClass: pass
```

### Q9: What is the difference between a decorator and a context manager?
- Decorator: wraps a function to add behavior.
- Context manager: uses `with` block for setup/cleanup.
Both are used for cross-cutting concerns but have different syntax.

### Q10: How do you create a decorator that caches results?
```python
from functools import wraps
def cache(func):
    memo = {}
    @wraps(func)
    def wrapper(*args):
        if args not in memo:
            memo[args] = func(*args)
        return memo[args]
    return wrapper
```

### Q11: How do you debug a decorated function?
Use `functools.wraps` so the decorator preserves `__name__` and `__doc__`. Or use `inspect.unwrap()` to get the original function.

### Q12: Can a decorator be applied to a class method?
Yes. You may need to handle `self` carefully — the wrapper must accept `self`.

### Q13: What is a decorator factory?
A function that returns a decorator. Used when the decorator needs parameters.
```python
def prefix(prefix_str):
    def decorator(func):
        def wrapper(*args):
            return prefix_str + func(*args)
        return wrapper
    return decorator

@prefix("Mr. ")
def name(): return "John"
# Returns: "Mr. John"
```

### Q14: What is the `@wraps` decorator imported from?
`from functools import wraps`

### Q15: How do you write a decorator for async functions?
```python
def async_timer(func):
    async def wrapper(*args, **kwargs):
        start = time.time()
        result = await func(*args, **kwargs)
        print(f"Took {time.time() - start}s")
        return result
    return wrapper
```

### Q16: What is the purpose of `__call__` in decorators?
Classes with `__call__` can be used as decorators.
```python
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0
    def __call__(self, *args, **kwargs):
        self.count += 1
        return self.func(*args, **kwargs)
```

### Q17: How do you preserve signature of decorated function?
Use `functools.wraps` (preserves `__name__`, `__doc__`, `__module__`, `__annotations__`). For full signature preservation, use `functools.wraps` with `functools.update_wrapper`.

### Q18: What is the `singledispatch` decorator?
Allows function overloading based on first argument type.
```python
from functools import singledispatch

@singledispatch
def process(arg):
    print(f"Default: {arg}")

@process.register(int)
def _(arg):
    print(f"Integer: {arg}")

process("hello")  # Default: hello
process(42)       # Integer: 42
```

### Q19: Can decorators change the return value?
Yes. The wrapper can modify what the function returns.
```python
def uppercase(func):
    def wrapper(*args):
        return func(*args).upper()
    return wrapper
```

### Q20: What is the difference between decorator and inheritance?
- Decorator: adds behavior to a specific function (composition at function level).
- Inheritance: creates a new class that extends parent (class-level reuse).
Decorator is more flexible — you can apply multiple decorators independently.
