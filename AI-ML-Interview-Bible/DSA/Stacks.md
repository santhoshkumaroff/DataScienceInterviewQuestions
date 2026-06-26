# Stacks

## What is it?
A stack is a Last-In-First-Out (LIFO) data structure. Elements are added (push) and removed (pop) from the top. Python lists work perfectly as stacks using append() and pop(). The most recently added item is the first one to be removed.

## Why do we use it?
Stacks are critical for expression evaluation in ML compilers and interpreters. They power undo/redo in data science notebooks, manage function calls in recursion (call stack), and are used in depth-first search for graph traversal and tree exploration.

## Real-Time Example 1
**Compiler for ML framework (TensorFlow graph):** When parsing an expression like `y = (x + 3) * 2`, the compiler uses a stack to validate parentheses matching. A monotonic stack optimizes computation order by finding next greater/smaller operations. Stack frames track nested function calls during autograd (automatic differentiation).

## Real-Time Example 2
**Browser history in web scraping tool:** A web scraper crawls pages using DFS (stack). Each page URL is pushed onto a stack. When a dead end is reached, the scraper pops the stack to backtrack. Undo functionality in Jupyter notebooks uses a stack of cell states for Ctrl+Z operations.

## Concept Example 1
```python
# Balanced parentheses using stack
def is_balanced(s):
    stack = []
    pairs = {')': '(', '}': '{', ']': '['}
    for c in s:
        if c in pairs.values():
            stack.append(c)
        elif c in pairs:
            if not stack or stack.pop() != pairs[c]:
                return False
    return len(stack) == 0

print(is_balanced("({[]})"))  # Output: True
print(is_balanced("({[})"))   # Output: False
```
**Input:** "({[]})"
**Output:** True
**Explanation:** Push opening brackets. When closing bracket found, check if top matches. Stack must be empty at end. O(n) time, O(n) space.

## Concept Example 2
```python
# Monotonic stack: Next Greater Element
def next_greater(arr):
    result = [-1] * len(arr)
    stack = []
    for i in range(len(arr)):
        while stack and arr[i] > arr[stack[-1]]:
            idx = stack.pop()
            result[idx] = arr[i]
        stack.append(i)
    return result

print(next_greater([4, 2, 6, 8, 1]))  # Output: [6, 6, 8, -1, -1]
```
**Input:** [4, 2, 6, 8, 1]
**Output:** [6, 6, 8, -1, -1]
**Explanation:** Maintain decreasing stack. When larger element found, pop all smaller and assign as their next greater. O(n) time.

## Common Mistakes
- Forgetting stack can be empty before popping (causes IndexError)
- Using stack when queue is needed (LIFO vs FIFO confusion)
- Not handling edge cases with nested structures
- Overlooking that recursion itself uses stack (deep recursion = stack overflow)

## How Interviewers Ask This
- "Implement a stack with getMin() in O(1)"
- "Valid parentheses with multiple bracket types"
- "Evaluate Reverse Polish Notation"
- "Implement queue using stacks"

## How To Impress The Interviewer
- Know that monotonic stack solves Next Greater Element, Largest Rectangle, Daily Temperatures in O(n)
- Mention stack is used in ML for backpropagation call chain
- Understand recursion-to-iteration conversion using explicit stack
- For AI/ML: stack frames store variable scope in computational graphs

## Most Asked Questions

**1. Valid Parentheses**  
*Q:* Check if string with '(){}[]' is balanced.  
*A:*
```python
def is_valid(s):
    stack = []
    m = {')': '(', '}': '{', ']': '['}
    for c in s:
        if c in m:
            if not stack or stack.pop() != m[c]:
                return False
        else:
            stack.append(c)
    return not stack
```

**2. Min Stack**  
*Q:* Design stack with push, pop, top, getMin in O(1).  
*A:*
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []
    def push(self, val):
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
    def pop(self):
        if self.stack.pop() == self.min_stack[-1]:
            self.min_stack.pop()
    def top(self):
        return self.stack[-1]
    def get_min(self):
        return self.min_stack[-1]
```

**3. Evaluate Reverse Polish Notation**  
*Q:* Evaluate tokens like ["2","1","+","3","*"] → 9.  
*A:*
```python
def eval_rpn(tokens):
    stack = []
    for t in tokens:
        if t in '+-*/':
            b, a = stack.pop(), stack.pop()
            if t == '+': stack.append(a + b)
            elif t == '-': stack.append(a - b)
            elif t == '*': stack.append(a * b)
            else: stack.append(int(a / b))
        else:
            stack.append(int(t))
    return stack[0]
```

**4. Daily Temperatures**  
*Q:* Days until warmer temperature for each day.  
*A:*
```python
def daily_temps(temps):
    res = [0] * len(temps)
    stack = []
    for i, t in enumerate(temps):
        while stack and t > temps[stack[-1]]:
            idx = stack.pop()
            res[idx] = i - idx
        stack.append(i)
    return res
```

**5. Implement Queue Using Stacks**  
*Q:* Use two stacks to implement FIFO queue.  
*A:*
```python
class MyQueue:
    def __init__(self):
        self.s1, self.s2 = [], []
    def push(self, x):
        self.s1.append(x)
    def pop(self):
        self.peek()
        return self.s2.pop()
    def peek(self):
        if not self.s2:
            while self.s1:
                self.s2.append(self.s1.pop())
        return self.s2[-1]
    def empty(self):
        return not self.s1 and not self.s2
```

**6. Largest Rectangle in Histogram**  
*Q:* Find largest rectangle area in histogram (hard).  
*A:*
```python
def largest_rectangle(heights):
    stack = []
    max_area = 0
    heights.append(0)
    for i, h in enumerate(heights):
        while stack and h < heights[stack[-1]]:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    return max_area
```

**7. Decode String**  
*Q:* Decode "3[a2[c]]" → "accaccacc".  
*A:*
```python
def decode_string(s):
    stack = []
    for c in s:
        if c != ']':
            stack.append(c)
        else:
            substr = ''
            while stack[-1] != '[':
                substr = stack.pop() + substr
            stack.pop()
            num = ''
            while stack and stack[-1].isdigit():
                num = stack.pop() + num
            stack.append(int(num) * substr)
    return ''.join(stack)
```

**8. Next Greater Element I**  
*Q:* Find next greater for nums1 elements in nums2.  
*A:*
```python
def next_greater(nums1, nums2):
    mapping = {}
    stack = []
    for num in nums2:
        while stack and num > stack[-1]:
            mapping[stack.pop()] = num
        stack.append(num)
    return [mapping.get(n, -1) for n in nums1]
```

**9. Simplify Path**  
*Q:* Convert "/a/./b/../../c/" to canonical path.  
*A:*
```python
def simplify_path(path):
    stack = []
    for p in path.split('/'):
        if p == '..':
            if stack: stack.pop()
        elif p and p != '.':
            stack.append(p)
    return '/' + '/'.join(stack)
```

**10. Asteroid Collision**  
*Q:* Asteroids moving left/right, smaller explodes. Return final state.  
*A:*
```python
def asteroid_collision(asteroids):
    stack = []
    for a in asteroids:
        while stack and a < 0 < stack[-1]:
            if stack[-1] < -a:
                stack.pop()
                continue
            elif stack[-1] == -a:
                stack.pop()
            break
        else:
            stack.append(a)
    return stack
```
