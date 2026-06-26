# Exception Handling

## What is it?
Exception handling manages runtime errors gracefully using `try`, `except`, `else`, and `finally`. Instead of crashing, the program catches errors and responds appropriately. Custom exceptions are user-defined error classes. Best practices include catching specific exceptions and never hiding bugs with bare `except:`.

## Why do we use it?
In production AI/ML systems, unexpected errors happen — missing files, bad data, network failures. Exception handling keeps systems running, helps debugging, and provides meaningful error messages to users.

## Real-Time Example 1
**Payment Processing (Fintech)**

Scenario: A payment gateway processes transactions and must handle failures gracefully.

Step 1: Try to process payment via API call.
Step 2: Catch network timeout (`requests.exceptions.Timeout`).
Step 3: Catch invalid card details (`ValueError`).
Step 4: Catch insufficient balance (custom `InsufficientFundsError`).
Step 5: Finally block logs the transaction attempt.

```python
class InsufficientFundsError(Exception):
    pass

def process_payment(amount, balance):
    if amount > balance:
        raise InsufficientFundsError("Not enough balance")
    return "Payment successful"

try:
    result = process_payment(500, 200)
    print(result)
except InsufficientFundsError as e:
    print(f"Payment failed: {e}")
finally:
    print("Transaction logged")
```

## Real-Time Example 2
**Data Pipeline (ML/AI)**

Scenario: A machine learning pipeline reads data from multiple sources and must handle missing files gracefully.

Step 1: Try to read training data from CSV.
Step 2: If file not found, try backup location.
Step 3: If data format is wrong, log error and skip file.
Step 4: Use else block to confirm successful load.
Step 5: Use finally to close all open connections.

```python
def load_data(filepath):
    try:
        with open(filepath, "r") as f:
            data = f.read()
    except FileNotFoundError:
        print(f"File {filepath} not found, trying backup...")
        with open("backup.csv", "r") as f:
            data = f.read()
    except PermissionError:
        print("Permission denied, skipping...")
        return None
    else:
        print("Data loaded successfully")
        return data
    finally:
        print("Cleanup complete")
```

## Concept Example 1
**Basic Try/Except/Finally**

```python
try:
    num = int(input("Enter a number: "))
    result = 10 / num
    print(f"Result: {result}")
except ValueError:
    print("That's not a valid number!")
except ZeroDivisionError:
    print("Cannot divide by zero!")
else:
    print("No errors occurred.")
finally:
    print("This always runs.")
```

Input: User enters "0".
Output:
```
Enter a number: 0
Cannot divide by zero!
This always runs.
```
Explanation: `try` runs the risky code. If `ZeroDivisionError` occurs, the matching except block runs. `else` runs if no error. `finally` always runs (cleanup).

## Concept Example 2
**Custom Exception with Raising**

```python
class AgeError(Exception):
    def __init__(self, age, message="Age must be between 18 and 60"):
        self.age = age
        self.message = message
        super().__init__(self.message)

def set_age(age):
    if age < 18 or age > 60:
        raise AgeError(age)
    return f"Age {age} set successfully"

try:
    print(set_age(15))
except AgeError as e:
    print(f"Error: {e}")
```

Input: None (hardcoded age=15).
Output:
```
Error: Age must be between 18 and 60
```
Explanation: Custom `AgeError` class extends `Exception`. It provides domain-specific error handling with meaningful messages.

## Common Mistakes
1. Using bare `except:` — catches ALL errors including KeyboardInterrupt, SystemExit (use `except Exception:` instead).
2. Not catching specific exceptions first (order matters — child before parent).
3. Swallowing exceptions with `pass` in except block (makes debugging hard).
4. Raising `Exception` instead of specific exception types.
5. Forgetting `finally` for resource cleanup (files, DB connections).

## How Interviewers Ask This
1. "What is the difference between `try`, `except`, `else`, and `finally`?"
2. "How do you create a custom exception?"
3. "What is exception chaining?"
4. "How do you handle multiple exceptions in one line?"
5. "What is the `raise` keyword used for?"

## How To Impress The Interviewer
1. Know that `finally` runs even if there's a `return` in `try` or `except` (runs before return).
2. Use specific exception types (`ValueError`, `TypeError`, `FileNotFoundError`) for precise handling.
3. Mention exception chaining using `raise ... from e` to preserve the original traceback.
4. Know the difference between `Exception` and `BaseException` — never catch `BaseException`.
5. Use `contextlib.suppress()` to ignore specific exceptions cleanly.

## Most Asked Questions

### Q1: What is the difference between `try`, `except`, `else`, `finally`?
- `try`: contains code that may raise an exception.
- `except`: handles specific exceptions.
- `else`: runs if NO exception occurred in try.
- `finally`: ALWAYS runs (cleanup).
```python
try: x = 1 / 0
except ZeroDivisionError: print("error")
else: print("success")
finally: print("cleanup")
```

### Q2: How do you create a custom exception?
```python
class MyError(Exception):
    def __init__(self, message):
        super().__init__(message)

raise MyError("Something went wrong")
```
Real example: Custom `ValidationError` in Django/Flask.

### Q3: What is the difference between `raise` and `assert`?
- `raise`: explicitly raises an exception.
- `assert`: raises `AssertionError` if condition is False (used for debugging).
```python
assert x > 0, "x must be positive"  # raises AssertionError
if x <= 0: raise ValueError("x must be positive")
```

### Q4: What is the purpose of `finally`?
Cleanup operations like closing files, database connections, releasing locks. Runs whether exception occurred or not.
```python
f = open("file.txt")
try:
    f.read()
finally:
    f.close()  # always executed
```

### Q5: Can you have multiple except blocks?
Yes. Order from most specific to most general.
```python
try:
    x = int("abc")
except ValueError:
    print("Value error")
except TypeError:
    print("Type error")
except Exception:
    print("General error")
```

### Q6: What is exception chaining?
Raising a new exception while preserving the original.
```python
try:
    int("abc")
except ValueError as e:
    raise RuntimeError("Conversion failed") from e
```
Original traceback is preserved.

### Q7: What is the `else` block in exception handling?
Runs when `try` block completes without any exception.
```python
try:
    x = int("10")
except ValueError:
    print("error")
else:
    print(f"success: {x}")  # runs
```

### Q8: How do you handle multiple exception types in one line?
```python
try:
    x = int("abc")
except (ValueError, TypeError) as e:
    print(f"Caught: {e}")
```

### Q9: What happens if an exception is not caught?
It propagates up the call stack until caught. If never caught, the program crashes with traceback.

### Q10: What is `SystemExit` and `KeyboardInterrupt`?
- `SystemExit`: raised by `sys.exit()`.
- `KeyboardInterrupt`: raised by Ctrl+C.
Both inherit from `BaseException`, not `Exception`. Never catch them accidentally.

### Q11: What is `assert` used for?
Debugging and testing. Raises `AssertionError` if condition is False.
```python
def divide(a, b):
    assert b != 0, "Division by zero"
    return a / b
```

### Q12: What is `try-finally` without `except`?
Used when you want cleanup but want exceptions to propagate.
```python
try:
    resource.acquire()
    do_work()
finally:
    resource.release()  # always released
```

### Q13: How do you get exception info using `sys.exc_info()`?
```python
import sys
try:
    1 / 0
except:
    exc_type, exc_value, exc_traceback = sys.exc_info()
```

### Q14: What is `contextlib.suppress`?
Ignores specific exceptions without writing try/except.
```python
from contextlib import suppress
with suppress(FileNotFoundError):
    os.remove("file.txt")
```

### Q15: What is the difference between `Exception` and `BaseException`?
- `BaseException`: base for ALL exceptions (including SystemExit, KeyboardInterrupt).
- `Exception`: base for all non-system exceptions. Always catch `Exception`, not `BaseException`.

### Q16: How do you re-raise an exception?
```python
try:
    int("abc")
except ValueError:
    print("logging...")
    raise  # re-raises the same exception
```

### Q17: What is `StopIteration`?
Raised when an iterator is exhausted. Used internally by `for` loops.

### Q18: What happens in nested try/except?
Inner exceptions can be caught by inner handler or propagate to outer. Finally blocks run for each.

### Q19: How to handle file not found error?
```python
try:
    with open("missing.txt") as f:
        pass
except FileNotFoundError:
    print("File not found, creating...")
    with open("missing.txt", "w") as f:
        f.write("")
```

### Q20: What is `Warning` in Python?
Base class for warnings (not exceptions). Use `warnings.warn()` for non-critical issues.
```python
import warnings
warnings.warn("Deprecated function", DeprecationWarning)
```
