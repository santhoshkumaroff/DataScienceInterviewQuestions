# Trees

## What is it?
A tree is a hierarchical data structure with a root node and child nodes forming parent-child relationships. Binary trees have at most two children (left and right). Binary Search Trees (BST) maintain the property: left < root < right. Trees model any hierarchical data.

## Why do we use it?
Trees are everywhere in AI/ML: decision trees and random forests are ML models built on tree structures. Expression trees represent mathematical formulas in symbolic computation. Hierarchical clustering uses dendrograms. File systems, HTML DOM, and organizational charts are all trees.

## Real-Time Example 1
**Recommendation system using decision trees:** User features (age, location, browsing history) are passed through decision tree nodes. At each node, a condition splits the data (e.g., age > 25). Tree traversal (DFS) finds leaf nodes containing product recommendations. Ensemble methods like Random Forest combine multiple trees.

## Real-Time Example 2
**AutoML pipeline for hyperparameter search:** Hyperparameter configurations form a tree of choices — first choose model type, then learning rate, then batch size. Level-order traversal evaluates all combos at same depth. BST organizes hyperparameter values for fast lookup. LCA finds common ancestor of two configs.

## Concept Example 1
```python
# Binary tree inorder traversal (DFS)
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorder_traversal(root):
    result = []
    def dfs(node):
        if not node:
            return
        dfs(node.left)
        result.append(node.val)
        dfs(node.right)
    dfs(root)
    return result

root = TreeNode(1, None, TreeNode(2, TreeNode(3)))
print(inorder_traversal(root))  # Output: [1, 3, 2]
```
**Input:** Tree: 1 → right → 2 → left → 3
**Output:** [1, 3, 2]
**Explanation:** Inorder visits left, root, right. Recursive DFS uses implicit stack. O(n) time, O(h) space.

## Concept Example 2
```python
# Level order traversal (BFS)
from collections import deque

def level_order(root):
    if not root:
        return []
    res = []
    q = deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        res.append(level)
    return res

root = TreeNode(3, TreeNode(9), TreeNode(20, TreeNode(15), TreeNode(7)))
print(level_order(root))  # Output: [[3], [9, 20], [15, 7]]
```
**Input:** Tree: 3 (left:9, right:20 → left:15, right:7)
**Output:** [[3], [9, 20], [15, 7]]
**Explanation:** BFS using queue processes level by level. Outer loop continues until queue empty. O(n) time.

## Common Mistakes
- Forgetting base case (None check) in recursive functions
- Confusing inorder/preorder/postorder traversal orders
- Not handling BST property violations during insertion/deletion
- Using recursion without considering stack overflow for deep trees

## How Interviewers Ask This
- "Find maximum depth of binary tree"
- "Validate if tree is a BST"
- "Find lowest common ancestor of two nodes"
- "Serialize and deserialize binary tree"

## How To Impress The Interviewer
- Know all 4 traversals: inorder, preorder, postorder, level-order with both recursive and iterative
- Use DFS for depth-related, BFS for level-related problems
- Understand height-balanced vs unbalanced trees (balance factor)
- For AI/ML: connect to decision trees (splitting criteria, pruning), gradient boosting (additive trees), random forests

## Most Asked Questions

**1. Maximum Depth of Binary Tree**  
*Q:* Find maximum depth (root-to-leaf longest path).  
*A:*
```python
def max_depth(root):
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

**2. Validate BST**  
*Q:* Check if binary tree is a valid BST.  
*A:*
```python
def is_valid_bst(root, low=float('-inf'), high=float('inf')):
    if not root:
        return True
    if root.val <= low or root.val >= high:
        return False
    return (is_valid_bst(root.left, low, root.val) and
            is_valid_bst(root.right, root.val, high))
```

**3. Lowest Common Ancestor (BST)**  
*Q:* Find LCA of two nodes in BST.  
*A:*
```python
def lowest_common_ancestor(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root
```

**4. Lowest Common Ancestor (Binary Tree)**  
*Q:* Find LCA in general binary tree.  
*A:*
```python
def lowest_common_ancestor(root, p, q):
    if not root or root == p or root == q:
        return root
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    if left and right:
        return root
    return left or right
```

**5. Diameter of Binary Tree**  
*Q:* Longest path between any two nodes (may not pass root).  
*A:*
```python
def diameter_of_binary_tree(root):
    diameter = 0
    def height(node):
        nonlocal diameter
        if not node:
            return 0
        left = height(node.left)
        right = height(node.right)
        diameter = max(diameter, left + right)
        return 1 + max(left, right)
    height(root)
    return diameter
```

**6. Binary Tree Level Order Traversal**  
*Q:* Return level-by-level traversal as list of lists.  
*A:*
```python
from collections import deque
def level_order(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level)
    return res
```

**7. Invert Binary Tree**  
*Q:* Mirror the tree (swap left and right children).  
*A:*
```python
def invert_tree(root):
    if not root:
        return None
    root.left, root.right = invert_tree(root.right), invert_tree(root.left)
    return root
```

**8. Symmetric Tree**  
*Q:* Check if tree is mirror of itself.  
*A:*
```python
def is_symmetric(root):
    def mirror(t1, t2):
        if not t1 and not t2: return True
        if not t1 or not t2: return False
        return (t1.val == t2.val and
                mirror(t1.left, t2.right) and
                mirror(t1.right, t2.left))
    return mirror(root, root)
```

**9. Construct Binary Tree from Inorder and Preorder**  
*Q:* Build tree using inorder and preorder arrays.  
*A:*
```python
def build_tree(preorder, inorder):
    if not inorder:
        return None
    idx = inorder.index(preorder.pop(0))
    root = TreeNode(inorder[idx])
    root.left = build_tree(preorder, inorder[:idx])
    root.right = build_tree(preorder, inorder[idx+1:])
    return root
```

**10. Serialize and Deserialize Binary Tree**  
*Q:* Convert tree to string and back using level-order.  
*A:*
```python
from collections import deque
def serialize(root):
    if not root: return ''
    res, q = [], deque([root])
    while q:
        node = q.popleft()
        if node:
            res.append(str(node.val))
            q.append(node.left)
            q.append(node.right)
        else:
            res.append('null')
    return ','.join(res)

def deserialize(data):
    if not data: return None
    vals = data.split(',')
    root = TreeNode(int(vals[0]))
    q = deque([root])
    i = 1
    while q and i < len(vals):
        node = q.popleft()
        if vals[i] != 'null':
            node.left = TreeNode(int(vals[i]))
            q.append(node.left)
        i += 1
        if i < len(vals) and vals[i] != 'null':
            node.right = TreeNode(int(vals[i]))
            q.append(node.right)
        i += 1
    return root
```

**11. Kth Smallest Element in BST**  
*Q:* Find kth smallest value in BST (1-indexed).  
*A:*
```python
def kth_smallest(root, k):
    stack = []
    curr = root
    while stack or curr:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        k -= 1
        if k == 0:
            return curr.val
        curr = curr.right
```

**12. Path Sum**  
*Q:* Check if root-to-leaf path sums to target.  
*A:*
```python
def has_path_sum(root, target_sum):
    if not root:
        return False
    if not root.left and not root.right:
        return root.val == target_sum
    return (has_path_sum(root.left, target_sum - root.val) or
            has_path_sum(root.right, target_sum - root.val))
```
