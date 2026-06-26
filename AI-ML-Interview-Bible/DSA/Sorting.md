# Sorting

## What is it?
Sorting arranges elements in a specific order (ascending/descending). Different algorithms have different time/space complexities and stability guarantees. Python's built-in sort (Timsort) is O(n log n) and stable, but understanding internals is crucial for interviews and optimization.

## Why do we use it?
Sorting is foundational for ML pipelines. Feature sorting for tree-based models, ranking search results, organizing time-series data, nearest neighbor search (sort by distance), and data preprocessing all rely on sorting. Understanding sorting complexity helps optimize large-scale data processing.

## Real-Time Example 1
**E-commerce product ranking:** User searches "laptops under $1000". Products are sorted by relevance score (QuickSort for average O(n log n)). Some products have same score → stable sort preserves original order (Insertion Sort stable). Top 20 shown. Real-time updates need Merge Sort for external memory sorting.

## Real-Time Example 2
**Genomic data analysis:** DNA sequences are sorted by length and similarity. Merge Sort handles massive datasets that don't fit in memory (external sorting). Heap Sort sorts k-largest matches without full sort. Bubble/Selection too slow; QuickSort chosen for average-case efficiency.

## Concept Example 1
```python
# QuickSort implementation
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)

arr = [3, 6, 8, 10, 1, 2, 1]
print(quicksort(arr))  # Output: [1, 1, 2, 3, 6, 8, 10]
```
**Input:** [3, 6, 8, 10, 1, 2, 1]
**Output:** [1, 1, 2, 3, 6, 8, 10]
**Explanation:** Pick pivot, partition into less/equal/greater, recurse. O(n log n) average, O(n^2) worst (bad pivot choice). Not in-place.

## Concept Example 2
```python
# Merge Sort implementation
def mergesort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = mergesort(arr[:mid])
    right = mergesort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    res = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            res.append(left[i]); i += 1
        else:
            res.append(right[j]); j += 1
    return res + left[i:] + right[j:]

arr = [38, 27, 43, 3, 9, 82, 10]
print(mergesort(arr))  # Output: [3, 9, 10, 27, 38, 43, 82]
```
**Input:** [38, 27, 43, 3, 9, 82, 10]
**Output:** [3, 9, 10, 27, 38, 43, 82]
**Explanation:** Divide array in half, sort each half recursively, merge sorted halves. O(n log n) time, O(n) space. Stable.

## Common Mistakes
- Using O(n^2) algorithms (Bubble/Selection/Insertion) for large datasets
- Forgetting that Python's .sort() is in-place but sorted() returns new list
- Not understanding stability (equal elements keep original order)
- Assuming QuickSort is always O(n log n) — worst case is O(n^2) if pivot is always min/max

## How Interviewers Ask This
- "Implement QuickSort in-place"
- "Sort an array of 0s, 1s, and 2s (Dutch flag problem)"
- "Find kth largest element using QuickSelect"
- "Merge k sorted arrays"

## How To Impress The Interviewer
- Know complexity: Bubble/Selection/Insertion = O(n^2), Merge/Quick/Heap = O(n log n)
- In-place sorts: QuickSort (O(log n) stack), HeapSort (O(1))
- Stable sorts: Merge, Insertion, Bubble, Timsort (Python's default)
- For AI/ML: sorting is key for ranking, top-k selection, feature importance aggregation

## Most Asked Questions

**1. Bubble Sort**  
*Q:* Implement bubble sort.  
*A:*
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(n - i - 1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
                swapped = True
        if not swapped: break
    return arr
```
Time: O(n^2), Space: O(1), Stable

**2. Selection Sort**  
*Q:* Implement selection sort.  
*A:*
```python
def selection_sort(arr):
    for i in range(len(arr)):
        min_idx = i
        for j in range(i+1, len(arr)):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr
```
Time: O(n^2), Space: O(1), Not stable

**3. Insertion Sort**  
*Q:* Implement insertion sort.  
*A:*
```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j+1] = arr[j]
            j -= 1
        arr[j+1] = key
    return arr
```
Time: O(n^2), O(n) best, Space: O(1), Stable

**4. In-Place QuickSort**  
*Q:* Implement in-place quicksort (partition around pivot).  
*A:*
```python
def quicksort_inplace(arr, low=0, high=None):
    if high is None:
        high = len(arr) - 1
    if low < high:
        pi = partition(arr, low, high)
        quicksort_inplace(arr, low, pi-1)
        quicksort_inplace(arr, pi+1, high)

def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i+1], arr[high] = arr[high], arr[i+1]
    return i + 1
```

**5. Heap Sort**  
*Q:* Implement heap sort using max-heap.  
*A:*
```python
def heapify(arr, n, i):
    largest = i
    l, r = 2*i+1, 2*i+2
    if l < n and arr[l] > arr[largest]: largest = l
    if r < n and arr[r] > arr[largest]: largest = r
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)
    for i in range(n//2-1, -1, -1):
        heapify(arr, n, i)
    for i in range(n-1, 0, -1):
        arr[i], arr[0] = arr[0], arr[i]
        heapify(arr, i, 0)
    return arr
```
Time: O(n log n), Space: O(1), Not stable

**6. Sort Colors (Dutch Flag)**  
*Q:* Sort array of 0s, 1s, 2s in-place (one pass).  
*A:*
```python
def sort_colors(nums):
    low, mid, high = 0, 0, len(nums) - 1
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1; mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
```

**7. Merge k Sorted Arrays**  
*Q:* Merge k sorted arrays into one sorted array.  
*A:*
```python
import heapq
def merge_k_sorted(arrays):
    heap = []
    for i, arr in enumerate(arrays):
        if arr:
            heapq.heappush(heap, (arr[0], i, 0))
    res = []
    while heap:
        val, arr_idx, idx = heapq.heappop(heap)
        res.append(val)
        if idx + 1 < len(arrays[arr_idx]):
            heapq.heappush(heap, (arrays[arr_idx][idx+1], arr_idx, idx+1))
    return res
```

**8. Find Kth Largest (QuickSelect)**  
*Q:* Find kth largest element without full sort.  
*A:*
```python
import random
def find_kth_largest(nums, k):
    def quick_select(left, right, k_smallest):
        if left == right: return nums[left]
        pi = partition(nums, left, right)
        if k_smallest == pi: return nums[pi]
        elif k_smallest < pi: return quick_select(left, pi-1, k_smallest)
        else: return quick_select(pi+1, right, k_smallest)

    def partition(arr, low, high):
        pivot_idx = random.randint(low, high)
        arr[pivot_idx], arr[high] = arr[high], arr[pivot_idx]
        pivot = arr[high]
        i = low - 1
        for j in range(low, high):
            if arr[j] <= pivot:
                i += 1; arr[i], arr[j] = arr[j], arr[i]
        arr[i+1], arr[high] = arr[high], arr[i+1]
        return i + 1

    return quick_select(0, len(nums)-1, len(nums)-k)
```

**9. Counting Sort (for range-limited data)**  
*Q:* Sort array where values are in [0, k].  
*A:*
```python
def counting_sort(arr, k):
    count = [0] * (k + 1)
    for num in arr:
        count[num] += 1
    i = 0
    for num in range(k + 1):
        for _ in range(count[num]):
            arr[i] = num
            i += 1
    return arr
```
Time: O(n+k), Space: O(k), Stable if implemented with prefix sums

**10. TimSort (Python's built-in sort)**  
*Q:* What algorithm does Python's sort use?  
*A:* Python uses TimSort — hybrid of Merge Sort and Insertion Sort. O(n log n) worst case, O(n) best case (already sorted). Stable and adaptive. Detects already-sorted runs (galloping mode), uses Insertion Sort on small runs (minrun=32-64), then merges runs.

**Sorting Cheat Sheet:**

| Algorithm | Time (Avg) | Time (Worst) | Space | Stable |
|-----------|-----------|--------------|-------|--------|
| Bubble | O(n^2) | O(n^2) | O(1) | Yes |
| Selection | O(n^2) | O(n^2) | O(1) | No |
| Insertion | O(n^2) | O(n^2) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n^2) | O(log n) | No |
| Heap | O(n log n) | O(n log n) | O(1) | No |
| Counting | O(n+k) | O(n+k) | O(k) | Yes |
