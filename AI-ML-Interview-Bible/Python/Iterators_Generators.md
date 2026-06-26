# Iterators and Generators

## What is it?
An iterator is an object that implements `__iter__()` and `__next__()` to loop over elements. A generator is a function that uses `yield` instead of `return` — it pauses and resumes. Generator expressions are like list comprehensions but use `()` and are memory-efficient. Both provide lazy evaluation (produce values one at a time).

## Why do we use it?
In AI/ML, datasets can be gigabytes. Generators process data in chunks without loading everything into memory. Used in deep learning for batch generators, streaming data pipelines, and infinite sequences. They are memory-efficient and enable lazy computation.

## Real-Time Example 1
**Streaming Stock Prices (Finance)**

Scenario: A trading platform receives millions of stock price updates per second.

Step 1: Create a generator that yields stock prices from a data stream.
Step 2: Process one price at a time (calculate moving average).
Step 3: If memory error risk was high, generator prevents it by not storing all prices.
Step 4: Use `yield` to produce prices on demand.
Step 5: Filter prices using generator expression to detect anomalies.

```python
def stock_price_stream():
    prices = [100, 102, 101, 105, 103, 108]
    for price in prices:
        yield price  # produces one value at a time

stream = stock_price_stream()
for price in stream:
    if price > 105:
        print(f"Alert: {price}")  # Alert: 108
```

## Real-Time Example 2
**Image Data Generator (Deep Learning)**

Scenario: Training a neural network on 100,000 images that cannot fit in RAM.

Step 1: Create a generator that reads images one batch at a time.
Step 2: On each `next()` call, load and yield one batch of images.
Step 3: Apply data augmentation (flip, rotate) on each batch.
Step 4: Keep training loop running indefinitely using infinite generator.
Step 5: When all batches are consumed, restart from beginning.

```python
def image_batch_generator(image_paths, batch_size=32):
    batch = []
    for path in image_paths:
        # Simulate loading image
        image = f"image_data_{path}"
        batch.append(image)
        if len(batch) == batch_size:
            yield batch
            batch = []
    if batch:  # last incomplete batch
        yield batch

images = list(range(100))  # 100 images
batches = image_batch_generator(images, 32)
for i, batch in enumerate(batches):
    print(f"Batch {i}: {len(batch)} images")
```

## Concept Example 1
**Custom Iterator vs Generator**

```python
# Custom Iterator
class Squares:
    def __init__(self, n):
        self.n = n
        self.i = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.i >= self.n:
            raise StopIteration
        result = self.i ** 2
        self.i += 1
        return result

# Generator (same thing, simpler)
def squares_gen(n):
    for i in range(n):
        yield i ** 2

# Both work the same
print(list(Squares(5)))       # [0, 1, 4, 9, 16]
print(list(squares_gen(5)))   # [0, 1, 4, 9, 16]
```

Input: None.
Output:
```
[0, 1, 4, 9, 16]
[0, 1, 4, 9, 16]
```
Explanation: Both produce same result. Generator is simpler — `yield` handles `__iter__` and `__next__` automatically. The iterator approach needs manual `StopIteration` handling.

## Concept Example 2
**Generator Expression Memory Efficiency**

```python
import sys

# List comprehension — stores all in memory
list_squares = [x**2 for x in range(10000)]
print(f"List size: {sys.getsizeof(list_squares)} bytes")

# Generator expression — lazy, produces on demand
gen_squares = (x**2 for x in range(10000))
print(f"Gen size: {sys.getsizeof(gen_squares)} bytes")

# Both produce same output
print(sum(list_squares) == sum(gen_squares))  # True
```

Input: None.
Output:
```
List size: 87616 bytes
Gen size: 200 bytes
True
```
Explanation: Generator expression uses 200 bytes regardless of range size. List comprehension uses 87KB for 10,000 items. Generator is much more memory efficient for large data.

## Common Mistakes
1. Confusing `yield` with `return` — `yield` pauses (doesn't exit), `return` exits completely.
2. Reusing a generator after it's exhausted (generators can only be iterated once).
3. Converting a generator to a list unnecessarily (loses memory advantage).
4. Forgetting `StopIteration` in custom iterators (causes infinite loop).
5. Using list comprehension when generator expression would suffice (wastes memory).

## How Interviewers Ask This
1. "What is the difference between an iterator and a generator?"
2. "What does the `yield` keyword do?"
3. "How is a generator different from a normal function?"
4. "What is a generator expression? Give an example."
5. "When would you use a generator instead of a list?"

## How To Impress The Interviewer
1. Explain the internal state: generator stores local variables and execution state between `yield` calls.
2. Mention `yield from` for delegating to sub-generator (Python 3.3+).
3. Know that `send()`, `throw()`, and `close()` are methods of generator objects (coroutines).
4. Discuss "lazy evaluation" and its benefits in streaming/big data.
5. Explain that a generator is a type of iterator, but not all iterators are generators.

## Most Asked Questions

### Q1: What is the difference between an iterator and a generator?
- Iterator: any object with `__iter__()` and `__next__()` methods.
- Generator: an iterator created with `yield` (a simpler way to make iterators).
All generators are iterators, but not vice versa.

### Q2: What does `yield` do?
Pauses the function, saves its state, and returns a value. On the next call, resumes from where it paused.
```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1
```

### Q3: How many times can you iterate over a generator?
Only once. After exhaustion, it raises `StopIteration`. You must create a new generator to iterate again.

### Q4: What is a generator expression?
A memory-efficient, lazy version of list comprehension.
```python
# List comprehension (eager)
squares = [x**2 for x in range(10)]

# Generator expression (lazy)
squares = (x**2 for x in range(10))
```

### Q5: What is the advantage of generators for large datasets?
They don't store all values in memory. Values are computed on-demand.
Real example: Processing a 10GB CSV file row by row using a generator.

### Q6: What is `yield from`?
Delegates yielding to a sub-iterator.
```python
def chain(*iterables):
    for it in iterables:
        yield from it  # yield all items from it

print(list(chain("abc", [1, 2, 3])))  # ['a', 'b', 'c', 1, 2, 3]
```

### Q7: How do you create an infinite generator?
```python
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1
```
Real example: Generating infinite stock ticker IDs.

### Q8: What is the `send()` method on generators?
Sends a value back into the generator (coroutine).
```python
def coroutine():
    while True:
        received = yield
        print(f"Received: {received}")

c = coroutine()
next(c)  # prime the generator
c.send("hello")  # Received: hello
```
Real example: Implementing state machines.

### Q9: What happens when a generator function contains `return`?
`return` in a generator stops iteration. The return value is stored in `StopIteration.value`.
```python
def gen_with_return():
    yield 1
    yield 2
    return "done"

g = gen_with_return()
print(list(g))  # [1, 2]  (return value is not yielded)
```

### Q10: What is the difference between `__iter__` and `__next__`?
- `__iter__`: returns the iterator object (used in `for` loops).
- `__next__`: returns the next value or raises `StopIteration`.
```python
class MyIter:
    def __iter__(self): return self
    def __next__(self): ...
```

### Q11: How do generators save memory?
Generator holds only one value at a time (current state). List stores all values. For `range(1000000)`, generator uses ~200 bytes, list uses ~8MB.

### Q12: What is `StopIteration`?
An exception raised when an iterator has no more items. `for` loops catch it automatically.

### Q13: Can a generator have multiple `yield` statements?
Yes. Each `yield` is a point where execution pauses. On next `next()`, it resumes after that yield.
```python
def multi_yield():
    yield "A"
    yield "B"
    yield "C"
```

### Q14: What is the difference between `return` and `yield`?
- `return`: exits the function and gives a value.
- `yield`: pauses the function, gives a value, and remembers where it paused.

### Q15: How do you convert a generator to a list?
```python
gen = (x**2 for x in range(5))
lst = list(gen)  # [0, 1, 4, 9, 16]
```
CAUTION: This loses the memory advantage of generators.

### Q16: How do you close a generator?
Use `gen.close()`. This raises `GeneratorExit` inside the generator. The generator cannot be used after closing.

### Q17: What is a pipelining with generators?
Chaining generators to process data in stages.
```python
def read(file): yield from open(file)
def filter_lines(lines): yield from (l for l in lines if "error" in l)
def format(lines): yield from (l.upper() for l in lines)

# Pipeline: read -> filter -> format
pipeline = format(filter_lines(read("log.txt")))
```

### Q18: What are the limitations of generators?
- Can only iterate once.
- No random access (cannot index).
- No length (`len()` not available).
- Not thread-safe for concurrent access.

### Q19: How do you measure generator performance?
```python
import timeit
# List comprehension
timeit.timeit('sum([x**2 for x in range(1000)])', number=10000)
# Generator expression
timeit.timeit('sum(x**2 for x in range(1000))', number=10000)
```
Generator expression is usually faster because it avoids list construction.

### Q20: What is the difference between `iter()` and `next()`?
- `iter()`: returns an iterator object from an iterable.
- `next()`: gets the next value from an iterator.
```python
it = iter([1, 2, 3])
print(next(it))  # 1
print(next(it))  # 2
```
