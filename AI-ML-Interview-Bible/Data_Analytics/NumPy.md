# NumPy

## What is it?
NumPy (Numerical Python) is the fundamental Python library for numerical computing. It provides the ndarray (N-dimensional array) object — a fast, memory-efficient container for homogeneous data. NumPy is the foundation for Pandas, SciPy, scikit-learn, and deep learning frameworks.

## Why do we use it?
Python lists are slow for mathematical operations. NumPy arrays are 10-100x faster because they use contiguous memory blocks and vectorized operations (C-level loops). Any data science work involving numbers, matrices, or linear algebra needs NumPy.

## Real-Time Example 1
**Business Scenario:** A logistics company needs to calculate Euclidean distances between 10,000 warehouse locations and 5,000 delivery points.
**Step-by-Step:**
1. `warehouses = np.random.rand(10000, 2)` — 10,000 (lat, lon) pairs.
2. `delivery_points = np.random.rand(5000, 2)` — 5,000 points.
3. `diff = warehouses[:, np.newaxis, :] - delivery_points[np.newaxis, :, :]` — broadcasting to create all pairs.
4. `distances = np.sqrt(np.sum(diff**2, axis=2))` — 10,000×5,000 distance matrix.
5. `closest = np.argmin(distances, axis=0)` — closest warehouse for each delivery point.
6. This runs in milliseconds. A Python loop would take minutes.

## Real-Time Example 2
**Industry:** Finance
**Use Case:** Calculate moving average crossover signals for stock trading.
1. `prices = np.loadtxt('stock_prices.csv', delimiter=',')[:, 4]` — load closing prices.
2. `short_ma = np.convolve(prices, np.ones(20)/20, mode='valid')` — 20-day moving average.
3. `long_ma = np.convolve(prices, np.ones(50)/50, mode='valid')` — 50-day moving average.
4. `signals = np.where(short_ma > long_ma, 1, 0)` — 1 if short above long (buy).
5. `trades = np.diff(signals)` — 1 = buy signal, -1 = sell signal.
6. `returns = np.diff(prices[-len(trades):]) * trades[:-1]` — strategy returns.

## Concept Example 1
**Broadcasting**
- **Input:** `A = np.array([[1, 2, 3], [4, 5, 6]])` (shape 2×3). `B = np.array([10, 20, 30])` (shape 3,).
- **Operation:** `A + B`
- **Output:** `[[11, 22, 33], [14, 25, 36]]`
- **Explanation:** Broadcasting "stretches" the smaller array (B) to match the larger one's shape without copying data. Dimensions are compatible when they are equal or one is 1. Avoids explicit loops.

## Concept Example 2
**Reshaping and Axis Operations**
- **Input:** `arr = np.arange(12)` — array [0, 1, 2, ..., 11].
- **Reshape:** `arr.reshape(3, 4)` — 3 rows, 4 columns.
- **Sum by axis:** `arr.sum(axis=0)` sums columns (down each column). `arr.sum(axis=1)` sums rows (across each row).
- **Explanation:** `reshape` changes shape without changing data. `axis=0` means "collapse rows" (operate vertically). `axis=1` means "collapse columns" (operate horizontally). Understanding axes is critical for all NumPy operations.

## Common Mistakes
- Confusing `axis=0` and `axis=1` (axis=0 = rows/down, axis=1 = columns/across).
- Using Python lists instead of NumPy arrays for numerical operations.
- Not specifying `dtype`, causing integer overflow or wrong precision.
- Forgetting that `reshape` returns a view (not a copy) — modifying affects original.
- Using loops instead of vectorized operations.

## How Interviewers Ask This
- "Explain broadcasting in NumPy with an example."
- "What is the difference between a view and a copy in NumPy?"
- "How do you create a 5x5 identity matrix?"
- "How do you find unique values and their counts in an array?"

## How To Impress The Interviewer
- Discuss stride tricks and memory layout (C-contiguous vs Fortran-contiguous).
- Know np.einsum for advanced tensor operations (like Einstein summation).
- Explain how to use np.where effectively for conditional logic.
- Mention performance: use `out` parameter for in-place operations to save memory.
- Understand ufunc.reduce, ufunc.accumulate for custom aggregations.

## Most Asked Questions

**1. What is an ndarray in NumPy?**
An ndarray (N-dimensional array) is a homogeneous, fixed-size container of elements of the same type. It has a shape (tuple of dimensions), dtype (element type), and strides (bytes to skip to reach next element in each dimension).

**2. What is the difference between a Python list and a NumPy array?**
NumPy arrays: homogeneous, contiguous memory, faster (C implementation), support vectorized operations, consume less memory, have methods like .sum(), .mean(), .reshape(). Python lists: heterogeneous, references to objects, slower, more flexible.

**3. How do you create a NumPy array?**
`np.array([1, 2, 3])` from list. `np.zeros((3, 4))` zeros. `np.ones((2, 3))` ones. `np.eye(5)` identity. `np.arange(0, 10, 2)` range. `np.linspace(0, 1, 5)` evenly spaced. `np.random.rand(3, 3)` uniform random.

**4. What is broadcasting?**
Broadcasting allows arithmetic between arrays of different shapes. The smaller array is "broadcast" across the larger array. Rules: dimensions are compared from the right; dimensions are compatible if equal or one is 1. Example: `(3,4) + (4,)` → `(3,4) + (1,4)` → `(3,4)`.

**5. How do you index and slice NumPy arrays?**
`arr[2:5]`, `arr[1:4, 2:6]`, `arr[:, 1]`, `arr[arr > 5]` (boolean indexing), `arr[[1, 3, 5]]` (fancy indexing). Slicing returns a view (not a copy), fancy/boolean indexing returns a copy.

**6. What is the difference between a view and a copy?**
A view shares data with the original array (changing the view changes the original). Basic slicing creates a view. A copy has its own data (`.copy()`). Fancy indexing and boolean indexing return copies. Reshape returns a view when possible.

**7. What are universal functions (ufuncs)?**
ufuncs are element-wise functions that operate on ndarrays: `np.add`, `np.subtract`, `np.multiply`, `np.divide`, `np.sqrt`, `np.exp`, `np.log`, `np.sin`, `np.cos`. They are vectorized (C speed) and support broadcasting, reduce, accumulate, and outer operations.

**8. How do you find unique values and their counts?**
`np.unique(arr)` returns sorted unique values. `np.unique(arr, return_counts=True)` also returns counts. `np.unique(arr, return_inverse=True)` returns indices to reconstruct the array (useful for encoding categories as integers).

**9. How do you compute basic statistics with NumPy?**
`np.mean(arr)`, `np.median(arr)`, `np.std(arr)`, `np.var(arr)`, `np.min(arr)`, `np.max(arr)`, `np.percentile(arr, 75)`. All accept `axis` parameter: `np.mean(arr, axis=0)` mean of each column.

**10. What is np.where and how is it used?**
`np.where(condition, x, y)` returns x where condition is True, y otherwise. `np.where(arr > 0, arr, 0)` replaces negatives with 0. Without x and y: `np.where(arr > 0)` returns tuple of indices where condition is True.

**11. How do you concatenate and split arrays?**
`np.concatenate([a, b], axis=0)` stacks along existing axis. `np.vstack([a, b])` vertical, `np.hstack([a, b])` horizontal. `np.split(arr, 3)` splits into 3 parts. `np.array_split(arr, 3)` handles uneven splits.

**12. How do you handle missing values in NumPy?**
NumPy uses `np.nan` (not a number). `np.nanmean(arr)`, `np.nanstd(arr)`, `np.nansum(arr)`, `np.isnan(arr)` to check. `arr[~np.isnan(arr)]` to filter out. `np.nan_to_num(arr, nan=0)` to replace.

**13. What is the difference between np.dot, np.matmul, and @?**
`np.dot` computes dot product of vectors and matrix multiplication of 2D arrays. `np.matmul` and `@` (operator) handle matrix multiplication with broadcasting for higher dimensions. For 2D arrays, they're equivalent.

**14. How do you generate random numbers in NumPy?**
`np.random.rand(3, 3)` uniform [0, 1). `np.random.randn(100)` standard normal. `np.random.randint(0, 10, size=(3, 3))` integers. `np.random.choice([1,2,3], size=10, p=[0.5, 0.3, 0.2])` weighted sampling.

**15. What are the different ways to reshape an array?**
`.reshape(new_shape)` — total elements must match. `.resize(new_shape)` — fills with zeros if larger, truncates if smaller. `.flatten()` — 1D copy. `.ravel()` — 1D view (when possible). `.T` — transpose. `np.newaxis` — adds dimension.

**16. How do you sort a NumPy array?**
`np.sort(arr)` returns sorted copy. `arr.sort()` sorts in-place. `np.argsort(arr)` returns indices that sort the array. `np.lexsort((arr1, arr2))` sort by multiple keys. `np.partition(arr, k)` partially sorts for top-k elements.

**17. What is linear algebra support in NumPy?**
`np.linalg.inv` (inverse), `np.linalg.det` (determinant), `np.linalg.eig` (eigenvalues/vectors), `np.linalg.svd` (SVD), `np.linalg.solve` (solve linear systems), `np.linalg.qr` (QR decomposition), `np.linalg.norm` (norm).

**18. How do you save and load NumPy arrays?**
`np.save('arr.npy', arr)` — single array. `np.savez('arrs.npz', a=a, b=b)` — multiple arrays (compressed: `np.savez_compressed`). `np.load('arr.npy')` — load. `np.savetxt('arr.csv', arr, delimiter=',')` for CSV.

**19. What is the difference between np.array and np.asarray?**
Both convert to ndarray. `np.array` always creates a new array (copies). `np.asarray` avoids copy if the input is already an ndarray with matching dtype. Use `asarray` when you don't need to own the data.

**20. How do you compute element-wise operations?**
Use operators: `+`, `-`, `*`, `/`, `**`, `%` directly on arrays. Comparison: `>`, `<`, `==`, `!=` return boolean arrays. Logical: `np.logical_and`, `np.logical_or`, `np.logical_not`.

**21. What is np.meshgrid?**
`np.meshgrid(x, y)` returns coordinate matrices from coordinate vectors. Used for evaluating functions on a grid and contour/surface plots. Example: `X, Y = np.meshgrid(np.arange(5), np.arange(3))`.

**22. How do you clip and handle outliers with NumPy?**
`np.clip(arr, min_val, max_val)` limits values between min and max. `np.percentile(arr, [5, 95])` finds thresholds. Use boolean indexing to replace outliers with bounds.

**23. What is the difference between nditer and conventional loops?**
`np.nditer(arr)` iterates over array elements efficiently (C-level, works with broadcasting). Rarely needed — prefer vectorized operations. May appear in legacy code or when custom C-level iteration is needed.

**24. How do you compute cumulative sum and product?**
`np.cumsum(arr)` returns cumulative sum along axis. `np.cumprod(arr)` cumulative product. `np.diff(arr)` differences between consecutive elements. `np.gradient(arr)` gradient (central differences).

**25. How do you use einsum?**
`np.einsum('ij->i', arr)` sums rows. `np.einsum('ij,jk->ik', A, B)` matrix multiply. `np.einsum('ii->i', arr)` extracts diagonal. Einsum uses Einstein summation notation for any tensor operation with high expressiveness.
