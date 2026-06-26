# Searching

## What is it?
Searching finds an element in a data structure. Linear Search checks every element (O(n)). Binary Search works on sorted arrays by repeatedly dividing search space in half (O(log n)). Variants handle rotated arrays, unknown bounds, and finding first/last occurrences.

## Why do we use it?
Searching is critical in ML for hyperparameter tuning (grid search, random search — both use variants of binary search for optimal learning rate), nearest neighbor search in recommendation systems, database lookups in feature stores, and anomaly detection threshold optimization.

## Real-Time Example 1
**AutoML hyperparameter tuning:** Finding best learning rate uses binary search-like approach. Start with [0.001, 0.1], evaluate at 0.01, narrow to [0.001, 0.01] if loss decreases. Binary search on monotonic validation loss curve finds optimal learning rate in O(log n) evaluations instead of O(n) grid search.

## Real-Time Example 2
**Fraud detection threshold optimization:** Model outputs fraud probability scores. Need to find threshold that maximizes F1 score. Searching sorted scores array for optimal threshold uses binary search on ROC curve. Linear search finds first occurrence where precision drops below acceptable level.

## Concept Example 1
```python
# Binary search (classic)
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

arr = [1, 3, 5, 7, 9, 11]
print(binary_search(arr, 7))  # Output: 3
print(binary_search(arr, 4))  # Output: -1
```
**Input:** arr=[1,3,5,7,9,11], target=7
**Output:** 3 (index of 7)
**Explanation:** Repeatedly narrow window. Mid calculation prevents overflow. O(log n) time, O(1) space.

## Concept Example 2
```python
# Search in rotated sorted array
def search_rotated(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        if arr[left] <= arr[mid]:  # left half sorted
            if arr[left] <= target < arr[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:  # right half sorted
            if arr[mid] < target <= arr[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1

print(search_rotated([4,5,6,7,0,1,2], 0))  # Output: 4
```
**Input:** [4,5,6,7,0,1,2], target=0
**Output:** 4 (index of 0)
**Explanation:** One half is always fully sorted. Check if target in sorted half, else go to other half. O(log n).

## Common Mistakes
- Using linear search when array is sorted (binary search is O(log n) vs O(n))
- Off-by-one errors in binary search (left <= right vs left < right)
- Not handling duplicates correctly in first/last occurrence
- Forgetting to handle empty array or single element
- Mid overflow: (left+right)//2 vs left+(right-left)//2 (Python fine, Java/C++ concern)

## How Interviewers Ask This
- "Implement binary search (iterative and recursive)"
- "Find first and last occurrence of target in sorted array"
- "Search in rotated sorted array"
- "Find peak element in mountain array"

## How To Impress The Interviewer
- Write binary search without bugs: left<=right, mid calc, eliminate half correctly
- Know lower_bound / upper_bound patterns (first/last occurrence)
- Apply binary search on answer (find smallest/largest value satisfying condition)
- For AI/ML: binary search for threshold optimization, hyperparameter search, monotonic loss functions

## Most Asked Questions

**1. Binary Search (Iterative)**  
*Q:* Implement binary search on sorted array.  
*A:*
```python
def binary_search(arr, target):
    l, r = 0, len(arr) - 1
    while l <= r:
        mid = (l + r) // 2
        if arr[mid] == target: return mid
        elif arr[mid] < target: l = mid + 1
        else: r = mid - 1
    return -1
```

**2. Binary Search (Recursive)**  
*Q:* Recursive binary search.  
*A:*
```python
def binary_search_rec(arr, target, l=0, r=None):
    if r is None: r = len(arr) - 1
    if l > r: return -1
    mid = (l + r) // 2
    if arr[mid] == target: return mid
    elif arr[mid] < target:
        return binary_search_rec(arr, target, mid+1, r)
    else:
        return binary_search_rec(arr, target, l, mid-1)
```

**3. First and Last Position of Element in Sorted Array**  
*Q:* Find first and last occurrence of target (O(log n)).  
*A:*
```python
def search_range(nums, target):
    def find_bound(is_first):
        l, r = 0, len(nums) - 1
        bound = -1
        while l <= r:
            mid = (l + r) // 2
            if nums[mid] == target:
                bound = mid
                if is_first: r = mid - 1
                else: l = mid + 1
            elif nums[mid] < target:
                l = mid + 1
            else:
                r = mid - 1
        return bound
    return [find_bound(True), find_bound(False)]
```

**4. Search in Rotated Sorted Array**  
*Q:* Search in rotated sorted array with O(log n).  
*A:*
```python
def search_rotated(nums, target):
    l, r = 0, len(nums) - 1
    while l <= r:
        mid = (l + r) // 2
        if nums[mid] == target: return mid
        if nums[l] <= nums[mid]:
            if nums[l] <= target < nums[mid]: r = mid - 1
            else: l = mid + 1
        else:
            if nums[mid] < target <= nums[r]: l = mid + 1
            else: r = mid - 1
    return -1
```

**5. Find Peak Element**  
*Q:* Find any peak (element > neighbors) in O(log n).  
*A:*
```python
def find_peak(nums):
    l, r = 0, len(nums) - 1
    while l < r:
        mid = (l + r) // 2
        if nums[mid] > nums[mid+1]:
            r = mid
        else:
            l = mid + 1
    return l
```

**6. Find Minimum in Rotated Sorted Array**  
*Q:* Find minimum element in rotated sorted array.  
*A:*
```python
def find_min(nums):
    l, r = 0, len(nums) - 1
    while l < r:
        mid = (l + r) // 2
        if nums[mid] > nums[r]:
            l = mid + 1
        else:
            r = mid
    return nums[l]
```

**7. Search Insert Position**  
*Q:* Return index where target should be inserted to maintain sorted order.  
*A:*
```python
def search_insert(nums, target):
    l, r = 0, len(nums)
    while l < r:
        mid = (l + r) // 2
        if nums[mid] >= target:
            r = mid
        else:
            l = mid + 1
    return l
```

**8. Sqrt(x) using Binary Search**  
*Q:* Find square root of x rounded down to integer.  
*A:*
```python
def my_sqrt(x):
    if x < 2: return x
    l, r = 2, x // 2
    while l <= r:
        mid = (l + r) // 2
        sq = mid * mid
        if sq == x: return mid
        elif sq < x: l = mid + 1
        else: r = mid - 1
    return r
```

**9. Find K Closest Elements**  
*Q:* Find k closest elements to target in sorted array.  
*A:*
```python
def find_closest(arr, k, x):
    l, r = 0, len(arr) - k
    while l < r:
        mid = (l + r) // 2
        if x - arr[mid] > arr[mid + k] - x:
            l = mid + 1
        else:
            r = mid
    return arr[l:l+k]
```

**10. Binary Search on Infinite Array (Find position of 1 in binary sorted infinite array)**  
*Q:* Find first occurrence of 1 in infinite binary sorted array.  
*A:*
```python
def find_first_one(arr):
    # First find bounds
    l, r = 0, 1
    while arr[r] == 0:
        l = r
        r *= 2
    # Binary search in bounds
    idx = -1
    while l <= r:
        mid = (l + r) // 2
        if arr[mid] == 1:
            idx = mid
            r = mid - 1
        else:
            l = mid + 1
    return idx
```

**11. Median of Two Sorted Arrays**  
*Q:* Find median of two sorted arrays in O(log(min(n,m))).  
*A:*
```python
def find_median_sorted(nums1, nums2):
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    m, n = len(nums1), len(nums2)
    total = m + n
    half = (total + 1) // 2
    l, r = 0, m
    while l <= r:
        i = (l + r) // 2
        j = half - i
        if i < m and nums1[i] < nums2[j-1]:
            l = i + 1
        elif i > 0 and nums1[i-1] > nums2[j]:
            r = i - 1
        else:
            max_left = max(
                nums1[i-1] if i > 0 else float('-inf'),
                nums2[j-1] if j > 0 else float('-inf')
            )
            if total % 2: return max_left
            min_right = min(
                nums1[i] if i < m else float('inf'),
                nums2[j] if j < n else float('inf')
            )
            return (max_left + min_right) / 2
    return 0
```
