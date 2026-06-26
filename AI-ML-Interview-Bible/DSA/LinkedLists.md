# Linked Lists

## What is it?
A linked list is a linear data structure where elements (nodes) are connected via pointers. Each node contains data and a reference to the next node. Unlike arrays, linked lists don't require contiguous memory. Python doesn't have a built-in linked list, so we implement it using classes.

## Why do we use it?
Linked lists excel at dynamic memory allocation and efficient insertions/deletions (O(1) at head). In ML, linked lists are used in hash table collision resolution (chaining), memory management in TensorFlow/PyTorch computational graphs, and implementing queues/deques for data streaming pipelines.

## Real-Time Example 1
**Music streaming playlist:** Each song is a node with next pointer. User adds songs to front (O(1)) or removes songs during playback. Doubly linked list enables "previous song" feature. Cycle detection finds if playlist has a loop (corrupted pointer). Reversing playback order reverses the list.

## Real-Time Example 2
**Transaction ledger in banking:** Each transaction is a node linked to the next. Blockchains use a linked-list-like structure (each block has hash of previous). Merge sort on linked list organizes transactions by timestamp. Finding intersection point identifies common ancestor of two account histories.

## Concept Example 1
```python
# Singly linked list reversal
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_temp = curr.next
        curr.next = prev
        prev = curr
        curr = next_temp
    return prev

# Build: 1->2->3->None
head = ListNode(1, ListNode(2, ListNode(3)))
rev = reverse_list(head)
# rev is now 3->2->1->None
```
**Input:** Linked list 1→2→3
**Output:** 3→2→1
**Explanation:** Iteratively reverse pointers. Three pointers: prev, curr, next_temp. O(n) time, O(1) space.

## Concept Example 2
```python
# Floyd's Cycle Detection (Tortoise and Hare)
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False

# Cycle: 1->2->3->4->2 (back to node 2)
node2 = ListNode(2)
node3 = ListNode(3, ListNode(4, node2))
node2.next = node3
head = ListNode(1, node2)
print(has_cycle(head))  # Output: True
```
**Input:** Linked list with cycle back to node 2
**Output:** True
**Explanation:** Slow moves 1 step, fast moves 2 steps. If they meet, cycle exists. O(n) time, O(1) space.

## Common Mistakes
- Losing reference to next node before saving it (forgetting next_temp in reversal)
- Not handling empty list or single-node cases
- Infinite loops in cycle detection if pointers aren't advanced properly
- Forgetting that Python garbage collection doesn't remove "deleted" nodes from memory

## How Interviewers Ask This
- "Reverse a linked list (iterative and recursive)"
- "Detect cycle in linked list"
- "Find middle of linked list"
- "Merge two sorted linked lists"

## How To Impress The Interviewer
- Always draw the list before coding — track prev, curr, next visually
- Know both iterative and recursive solutions
- Use dummy nodes to simplify edge cases (e.g., removing head)
- For AI/ML roles: relate to graph traversal, adjacency lists, attention mechanisms
- Master slow/fast pointer technique for: middle, cycle, intersection, kth from end

## Most Asked Questions

**1. Reverse a Linked List**  
*Q:* Reverse singly linked list iteratively and recursively.  
*A:*
```python
# Recursive
def reverse_recursive(head):
    if not head or not head.next:
        return head
    new_head = reverse_recursive(head.next)
    head.next.next = head
    head.next = None
    return new_head
```

**2. Detect Cycle**  
*Q:* Return node where cycle begins, or None.  
*A:*
```python
def detect_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            slow = head
            while slow != fast:
                slow = slow.next
                fast = fast.next
            return slow
    return None
```

**3. Merge Two Sorted Lists**  
*Q:* Merge two sorted linked lists into one sorted list.  
*A:*
```python
def merge_two(l1, l2):
    dummy = curr = ListNode()
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    curr.next = l1 or l2
    return dummy.next
```

**4. Remove Nth Node From End**  
*Q:* Remove nth node from end in one pass.  
*A:*
```python
def remove_nth_from_end(head, n):
    dummy = ListNode(0, head)
    fast = slow = dummy
    for _ in range(n):
        fast = fast.next
    while fast.next:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next
```

**5. Find Middle of Linked List**  
*Q:* Return middle node (second middle if even length).  
*A:*
```python
def middle_node(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

**6. Intersection of Two Linked Lists**  
*Q:* Find node where two lists intersect.  
*A:*
```python
def get_intersection(headA, headB):
    a, b = headA, headB
    while a != b:
        a = a.next if a else headB
        b = b.next if b else headA
    return a
```

**7. Palindrome Linked List**  
*Q:* Check if linked list is palindrome in O(n) time, O(1) space.  
*A:*
```python
def is_palindrome(head):
    # Find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    # Reverse second half
    prev = None
    while slow:
        nxt = slow.next
        slow.next = prev
        prev = slow
        slow = nxt
    # Compare halves
    while prev:
        if head.val != prev.val:
            return False
        head = head.next
        prev = prev.next
    return True
```

**8. Add Two Numbers**  
*Q:* Two numbers stored as linked lists in reverse order, return sum as list.  
*A:*
```python
def add_two(l1, l2):
    dummy = curr = ListNode()
    carry = 0
    while l1 or l2 or carry:
        v1 = l1.val if l1 else 0
        v2 = l2.val if l2 else 0
        total = v1 + v2 + carry
        carry = total // 10
        curr.next = ListNode(total % 10)
        curr = curr.next
        if l1: l1 = l1.next
        if l2: l2 = l2.next
    return dummy.next
```

**9. Odd Even Linked List**  
*Q:* Group odd index nodes before even index nodes.  
*A:*
```python
def odd_even_list(head):
    if not head:
        return None
    odd = head
    even = head.next
    even_head = even
    while even and even.next:
        odd.next = even.next
        odd = odd.next
        even.next = odd.next
        even = even.next
    odd.next = even_head
    return head
```

**10. Sort Linked List (Merge Sort)**  
*Q:* Sort linked list in O(n log n) time using merge sort.  
*A:*
```python
def sort_list(head):
    if not head or not head.next:
        return head
    # Split
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    mid = slow.next
    slow.next = None
    # Sort halves
    left = sort_list(head)
    right = sort_list(mid)
    # Merge
    return merge_two(left, right)
```
