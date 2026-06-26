# Arrays

## What is it?
An array is a collection of elements stored at contiguous memory locations. It is the most fundamental data structure where each element can be accessed using an index. Arrays have fixed size in static languages, but Python lists are dynamic arrays that grow automatically.

## Why do we use it?
Arrays are everywhere in AI/ML. Feature vectors, image pixels (each image is a 2D array), time-series data, word embeddings — all stored as arrays. Fast indexing (O(1) access) makes them essential for numerical computing with NumPy, Pandas, and PyTorch.

## Real-Time Example 1
**E-commerce recommendation system:** A user's browsing history is stored as an array of product IDs. When generating recommendations, the system slides a window over this array to capture recent interactions, applies two-pointer technique to find product pairs frequently bought together, and uses subarray sums to calculate spending patterns.

## Real-Time Example 2
**Healthcare patient monitoring:** An ICU monitor records heart rate readings every second into an array. The sliding window technique computes rolling averages to detect arrhythmia. Two-pointer comparison identifies when vitals deviate from baseline. In-place operations normalize the data before feeding to a prediction model.

## Concept Example 1
```python
# Two-pointer: Find pair with given sum in sorted array
def find_pair(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return [arr[left], arr[right]]
        elif s < target:
            left += 1
        else:
            right -= 1
    return []

arr = [1, 2, 4, 6, 8, 10]
print(find_pair(arr, 10))  # Output: [2, 8]
```
**Input:** Sorted array [1,2,4,6,8,10], target=10
**Output:** [2, 8]
**Explanation:** Two pointers start at ends. Sum > target → move right pointer left. Sum < target → move left pointer right. O(n) time.

## Concept Example 2
```python
# Sliding window: Maximum sum subarray of size k
def max_sum_subarray(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)
    return max_sum

arr = [2, 1, 5, 1, 3, 2]
print(max_sum_subarray(arr, 3))  # Output: 9
```
**Input:** arr=[2,1,5,1,3,2], k=3
**Output:** 9
**Explanation:** Slide window of size 3. Add new element, remove old one. Subarray [5,1,3] gives sum 9. O(n) time.

## Common Mistakes
- Off-by-one errors in loop indices (forgetting array starts at 0)
- Modifying array while iterating (causes skipped elements or infinite loops)
- Not handling empty arrays or single-element cases
- Using O(n^2) brute force when sliding window or two-pointer works in O(n)

## How Interviewers Ask This
- "Find the contiguous subarray with maximum sum" (Kadane's Algorithm)
- "Rotate array by k positions to the right"
- "Move all zeros to end while maintaining relative order"
- "Find the first missing positive integer"

## How To Impress The Interviewer
- State time and space complexity for every approach
- Discuss trade-offs: O(1) extra space vs O(n) space
- Mention edge cases: empty array, single element, duplicates, all same values
- For AI/ML roles, connect to use-cases: sliding window for time-series, two-pointer for similarity search, in-place ops for memory-constrained deployments
- Know Kadane's Algorithm (O(n) for max subarray sum) and Boyer-Moore Voting (O(n) for majority element)

## Most Asked Questions

**1. Two Sum**  
*Q:* Return indices of two numbers that add to target.  
*A:*
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**2. Best Time to Buy and Sell Stock**  
*Q:* Max profit from one buy and one sell.  
*A:*
```python
def max_profit(prices):
    min_price = float('inf')
    max_profit = 0
    for p in prices:
        min_price = min(min_price, p)
        max_profit = max(max_profit, p - min_price)
    return max_profit
```

**3. Product of Array Except Self**  
*Q:* Return array where output[i] = product of all elements except nums[i] without division.  
*A:*
```python
def product_except_self(nums):
    n = len(nums)
    res = [1] * n
    left = 1
    for i in range(n):
        res[i] = left
        left *= nums[i]
    right = 1
    for i in range(n - 1, -1, -1):
        res[i] *= right
        right *= nums[i]
    return res
```

**4. Maximum Subarray (Kadane's Algorithm)**  
*Q:* Find contiguous subarray with largest sum.  
*A:*
```python
def max_subarray(nums):
    max_ending = max_so_far = nums[0]
    for num in nums[1:]:
        max_ending = max(num, max_ending + num)
        max_so_far = max(max_so_far, max_ending)
    return max_so_far
```

**5. Rotate Array**  
*Q:* Rotate array to the right by k steps in-place.  
*A:*
```python
def rotate(nums, k):
    k %= len(nums)
    def reverse(l, r):
        while l < r:
            nums[l], nums[r] = nums[r], nums[l]
            l, r = l + 1, r - 1
    reverse(0, len(nums) - 1)
    reverse(0, k - 1)
    reverse(k, len(nums) - 1)
```

**6. Move Zeroes**  
*Q:* Move all zeros to end while maintaining relative order of non-zero elements.  
*A:*
```python
def move_zeroes(nums):
    pos = 0
    for i in range(len(nums)):
        if nums[i] != 0:
            nums[pos], nums[i] = nums[i], nums[pos]
            pos += 1
```

**7. Find All Duplicates in Array**  
*Q:* Find all elements that appear twice in O(n) time and O(1) space.  
*A:*
```python
def find_duplicates(nums):
    res = []
    for num in nums:
        idx = abs(num) - 1
        if nums[idx] < 0:
            res.append(abs(num))
        nums[idx] = -nums[idx]
    return res
```

**8. Container With Most Water**  
*Q:* Find two lines that together with x-axis form container holding most water.  
*A:*
```python
def max_area(height):
    l, r = 0, len(height) - 1
    max_water = 0
    while l < r:
        h = min(height[l], height[r])
        max_water = max(max_water, h * (r - l))
        if height[l] < height[r]:
            l += 1
        else:
            r -= 1
    return max_water
```

**9. First Missing Positive**  
*Q:* Find smallest missing positive integer in O(n) time and O(1) space.  
*A:*
```python
def first_missing_positive(nums):
    n = len(nums)
    for i in range(n):
        while 1 <= nums[i] <= n and nums[nums[i] - 1] != nums[i]:
            nums[nums[i] - 1], nums[i] = nums[i], nums[nums[i] - 1]
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    return n + 1
```

**10. Subarray Sum Equals K**  
*Q:* Count subarrays whose sum equals k.  
*A:*
```python
def subarray_sum(nums, k):
    count = total = 0
    prefix = {0: 1}
    for num in nums:
        total += num
        if total - k in prefix:
            count += prefix[total - k]
        prefix[total] = prefix.get(total, 0) + 1
    return count
```
