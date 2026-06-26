# File Handling

## What is it?
File handling means reading from and writing to files using Python. You can open files in different modes: `r` (read), `w` (write), `a` (append), `rb`/`wb` (binary). Context managers (`with` statement) automatically close files. CSV and JSON are common data formats used in data science.

## Why do we use it?
In AI/ML, you work with datasets stored as CSV, JSON, or text files. File handling is essential for loading data, saving models, logging, and processing large files efficiently.

## Real-Time Example 1
**Customer Data Export (Banking)**

Scenario: A bank needs to export customer transactions to CSV for analysis.

Step 1: Open a CSV file in write mode using `with open()`.
Step 2: Write header row with column names.
Step 3: Loop through transaction data and write rows.
Step 4: Use `csv.writer` for proper formatting.
Step 5: Handle errors if file cannot be created.

```python
import csv

transactions = [
    {"id": 1, "name": "Alice", "amount": 5000},
    {"id": 2, "name": "Bob", "amount": 3000},
]

with open("transactions.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["id", "name", "amount"])
    writer.writeheader()
    writer.writerows(transactions)
```

## Real-Time Example 2
**Model Configuration (ML/AI)**

Scenario: An ML model reads hyperparameters from a JSON config file.

Step 1: Read JSON config file using `json.load()`.
Step 2: Extract hyperparameters (learning rate, epochs, batch size).
Step 3: Train model with those parameters.
Step 4: Save training logs to a text file.
Step 5: Save trained model configuration back to JSON.

```python
import json

config = {"learning_rate": 0.001, "epochs": 10, "batch_size": 32}

# Write config
with open("config.json", "w") as f:
    json.dump(config, f, indent=4)

# Read config
with open("config.json", "r") as f:
    loaded = json.load(f)
print(loaded["learning_rate"])  # 0.001
```

## Concept Example 1
**Reading and Writing Text Files**

```python
# Writing to file
with open("sample.txt", "w") as f:
    f.write("Hello, World!\n")
    f.write("This is line 2.")

# Reading from file
with open("sample.txt", "r") as f:
    content = f.read()
    print(content)
```

Input: None (creates sample.txt).
Output:
```
Hello, World!
This is line 2.
```
Explanation: `with` statement ensures the file is closed automatically. `"w"` overwrites, `"r"` reads. `f.read()` returns entire content as string.

## Concept Example 2
**CSV File Handling**

```python
import csv

# Writing CSV
with open("data.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["Name", "Age", "City"])
    writer.writerow(["Alice", 25, "NYC"])
    writer.writerow(["Bob", 30, "LA"])

# Reading CSV
with open("data.csv", "r") as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

Input: None.
Output:
```
['Name', 'Age', 'City']
['Alice', '25', 'NYC']
['Bob', '30', 'LA']
```
Explanation: `csv.reader` returns each row as a list. `csv.writer` converts lists to comma-separated lines. Always use `newline=""` when writing CSV.

## Common Mistakes
1. Forgetting to close files (use `with` to avoid this).
2. Using `"w"` when you meant `"a"` (overwrites existing data).
3. Not using `newline=""` when writing CSV (adds extra blank lines).
4. Trying to read a file that doesn't exist (causes `FileNotFoundError`).
5. Not using `encoding="utf-8"` for text files with special characters.

## How Interviewers Ask This
1. "How do you read a large file line by line?"
2. "What is a context manager? How does `with` work?"
3. "How do you parse JSON and CSV in Python?"
4. "What is the difference between `read()`, `readline()`, and `readlines()`?"
5. "How do you handle file not found errors?"

## How To Impress The Interviewer
1. Mention that `with` uses `__enter__` and `__exit__` magic methods (context manager protocol).
2. For large files, use `for line in f:` instead of `f.read()` to avoid memory issues.
3. Know the difference between `json.dumps()` (to string) and `json.dump()` (to file).
4. Mention `csv.DictReader` and `csv.DictWriter` for working with column names.
5. Show awareness of `pathlib.Path` for modern file path handling.

## Most Asked Questions

### Q1: How do you read a file in Python?
```python
with open("file.txt", "r") as f:
    content = f.read()
```

### Q2: What is the difference between `r`, `w`, `a`, `r+` modes?
- `r`: Read only (default). File must exist.
- `w`: Write only. Creates or overwrites file.
- `a`: Append. Writes to end of file. Creates if doesn't exist.
- `r+`: Read and write. File must exist.
Real example: Logging uses `"a"` to add new logs without deleting old ones.

### Q3: What is a context manager? How does `with` work?
A context manager handles resource setup and cleanup automatically. `with` calls `__enter__` on entry and `__exit__` on exit (even if error occurs).
```python
with open("f.txt", "r") as f:
    pass  # f automatically closed
```
Real example: Database connections, file handles, locks.

### Q4: How do you read a large file without memory issues?
```python
with open("large_file.txt", "r") as f:
    for line in f:  # reads one line at a time
        process(line)
```
Real example: Processing 10GB log file line by line.

### Q5: What is the difference between `read()`, `readline()`, and `readlines()`?
- `read(n)`: reads all or n characters.
- `readline()`: reads one line.
- `readlines()`: reads all lines into a list.
For large files, use `readline()` or iteration (not `readlines()`).

### Q6: How do you handle JSON in Python?
```python
import json
# Dict to JSON string
s = json.dumps({"name": "Alice"})
# JSON string to dict
d = json.loads(s)
# File to dict
with open("data.json") as f:
    d = json.load(f)
```

### Q7: How do you handle CSV with header row?
```python
with open("data.csv") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["Name"])  # access by column name
```
Real example: Reading Kaggle datasets.

### Q8: What is `seek()` and `tell()`?
- `tell()`: returns current file position.
- `seek(offset, from)`: moves file pointer to offset.
Used when you need to read a file partially or restart reading.

### Q9: How do you check if a file exists?
```python
import os
os.path.exists("file.txt")

# Modern way
from pathlib import Path
Path("file.txt").exists()
```

### Q10: How do you write to a file without overwriting?
Use `"a"` (append) mode:
```python
with open("log.txt", "a") as f:
    f.write("New log entry\n")
```

### Q11: What is `pickle` in Python?
Pickle serializes Python objects to bytes and back.
```python
import pickle
data = {"model": "rf", "score": 0.95}
with open("model.pkl", "wb") as f:
    pickle.dump(data, f)
with open("model.pkl", "rb") as f:
    loaded = pickle.load(f)
```
Real example: Saving trained ML models.

### Q12: How do you handle encoding issues?
```python
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()
```
Always specify encoding for text files with special characters.

### Q13: What is the difference between text mode and binary mode?
- Text mode (`"r"`): reads strings, handles newline conversion.
- Binary mode (`"rb"`): reads bytes, no conversion.
Use binary for images, audio, pickle files.

### Q14: How do you delete a file?
```python
import os
os.remove("file.txt")
```

### Q15: How do you rename a file?
```python
import os
os.rename("old.txt", "new.txt")
```

### Q16: How do you create a directory?
```python
import os
os.makedirs("data/raw", exist_ok=True)  # creates nested dirs
```

### Q17: How do you list all files in a directory?
```python
import os
files = os.listdir(".")
# or
from pathlib import Path
files = list(Path(".").glob("*.txt"))
```

### Q18: How do you read a file from a URL?
```python
import requests
response = requests.get("https://example.com/data.csv")
content = response.text
```

### Q19: What is `tempfile` used for?
Creates temporary files that are automatically deleted.
```python
import tempfile
with tempfile.NamedTemporaryFile(mode="w", delete=True) as f:
    f.write("temp data")
```
Real example: Storing intermediate ML pipeline data.

### Q20: How do you append to a JSON file?
JSON doesn't support appending directly. Read all, modify, write back:
```python
with open("data.json") as f:
    data = json.load(f)
data.append(new_item)
with open("data.json", "w") as f:
    json.dump(data, f, indent=4)
```
