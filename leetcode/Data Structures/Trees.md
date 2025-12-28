# Trees: An Overview

A tree is a hierarchical data structure consisting of nodes connected by edges. It is widely used in computer science for representing hierarchical relationships such as file systems, organizational structures, and decision-making processes.

---

## Key Concepts in Trees

- **Root Node:** The top node in the tree. Every tree has exactly one root.
- **Parent and Child:** Nodes connected directly by an edge, where one is the parent and the other is the child.
- **Leaf Nodes:** Nodes with no children.
- **Height of Tree:** The number of edges on the longest path from the root to a leaf.
- **Depth of Node:** The number of edges from the root to the node.
- **Binary Tree:** Each node has at most two children (left and right).
- **Binary Search Tree (BST):**  
  Left subtree contains values smaller than the node, right subtree contains values larger than the node.
- **N-ary Tree:** Nodes can have up to N children.
- **Balanced Tree:** Heights of left and right subtrees differ by at most one.
- **Complete Binary Tree:** All levels are full except possibly the last, which is filled left to right.
- **Full Binary Tree:** Every node has either 0 or 2 children.

---

## Types of Problems and Solutions in Trees

## 1. Traversal Problems

**Objective:** Visit all nodes of the tree in a specific order.

### Traversal Types
- Preorder: Root → Left → Right
- Inorder: Left → Root → Right
- Postorder: Left → Right → Root
- Level-order: Breadth-First Search (BFS)

---

### 1. Preorder Traversal (Root → Left → Right)

#### Recursive
```python
def preorder_traversal(root):
    if not root:
        return []
    return [root.val] + preorder_traversal(root.left) + preorder_traversal(root.right)
````

#### Iterative

```python
def preorder_traversal_iterative(root):
    if not root:
        return []
    stack, result = [root], []
    while stack:
        node = stack.pop()
        result.append(node.val)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    return result
```

---

### 2. Inorder Traversal (Left → Root → Right)

#### Recursive

```python
def inorder_traversal(root):
    if not root:
        return []
    return inorder_traversal(root.left) + [root.val] + inorder_traversal(root.right)
```

#### Iterative

```python
def inorder_traversal_iterative(root):
    stack, result = [], []
    current = root
    while stack or current:
        while current:
            stack.append(current)
            current = current.left
        current = stack.pop()
        result.append(current.val)
        current = current.right
    return result
```

---

### 3. Postorder Traversal (Left → Right → Root)

#### Recursive

```python
def postorder_traversal(root):
    if not root:
        return []
    return postorder_traversal(root.left) + postorder_traversal(root.right) + [root.val]
```

#### Iterative

```python
def postorder_traversal_iterative(root):
    if not root:
        return []
    stack, result = [root], []
    while stack:
        node = stack.pop()
        result.append(node.val)
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
    return result[::-1]
```

---

### 4. Level-order Traversal (BFS)

```python
from collections import deque

def level_order_traversal(root):
    if not root:
        return []
    queue = deque([root])
    result = []
    while queue:
        node = queue.popleft()
        result.append(node.val)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return result
```

---

## Traversal Complexity Summary

| Traversal | Time | Auxiliary Space | Notes                        |
| --------- | ---- | --------------- | ---------------------------- |
| BFS       | O(n) | O(w)            | w = max width                |
| Preorder  | O(n) | O(h)            | Worst case skewed tree       |
| Inorder   | O(n) | O(h)            | Stack or recursion           |
| Postorder | O(n) | O(h)            | Iterative may use two stacks |

---

## Tree Node Class

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

---

## 2. Search Problems

**Objective:** Find a specific node or value.

* Binary Tree: DFS or BFS
* BST: Use ordering property, time complexity O(h)

---

## 3. Tree Properties

**Objective:** Compute height, depth, diameter.

**Approach:** Recursive computation on left and right subtrees.

---

## 4. Lowest Common Ancestor (LCA)

**Objective:** Find the lowest common ancestor of two nodes.

**Approach:**

* Recursive search
* Parent pointers or paths

---

## 5. Serialization and Deserialization

**Objective:** Convert tree to string and back.

**Approach:**

* Preorder or level-order traversal
* Use null markers

---

## 6. Check Tree Properties

**Examples:**

* Balanced tree
* Valid BST
* Complete tree

**Approach:** Recursive checks with additional state.

---

## 7. Path Problems

**Examples:**

* Root-to-leaf path sum
* All paths equal to target

**Approach:** DFS with backtracking
Use prefix sums for optimization.

---

## 8. Tree Modification

**Examples:**

* Invert binary tree
* Flatten tree to linked list

**Approach:** Recursive in-place modification.

---

## 9. Specialized Trees

* Segment Tree: Range queries
* Trie: Prefix search
* AVL / Red-Black Trees: Self-balancing

---

## Techniques for Solving Tree Problems

### 1. Recursion

```python
def height(node):
    if not node:
        return 0
    return 1 + max(height(node.left), height(node.right))
```

### 2. Iterative DFS and BFS

```python
def is_valid_bst(root):
    stack, prev = [], None
    curr = root
    while stack or curr:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        if prev is not None and curr.val <= prev:
            return False
        prev = curr.val
        curr = curr.right
    return True
```

### 3. Dynamic Programming on Trees

Bottom-up computation, example: diameter.

### 4. Backtracking

Used in root-to-leaf path problems.

### 5. Divide and Conquer

Used in tree construction from traversals.

---

## Tips for Tree Problem Solving

* Visualize the tree
* Identify required traversal
* Handle edge cases:

  * Empty tree
  * Single node
  * Skewed tree
* Always define base cases
* Prefer iterative solutions for deep trees

---

## Binary Tree Traversal Example

### Sample Tree

```
        1
       / \
      2   3
     / \   \
    4   5   6
```

---

## Traversal Results

| Traversal | Order              |
| --------- | ------------------ |
| Preorder  | [1, 2, 4, 5, 3, 6] |
| Inorder   | [4, 2, 5, 1, 3, 6] |
| Postorder | [4, 5, 2, 6, 3, 1] |

---

## Test Code

```python
root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)
root.left.left = TreeNode(4)
root.left.right = TreeNode(5)
root.right.right = TreeNode(6)

print("Pre-order:", preorder_traversal_iterative(root))
print("In-order:", inorder_traversal_iterative(root))
print("Post-order:", postorder_traversal_iterative(root))
```

---

Mastering these concepts will prepare you to solve most tree-related coding problems effectively.


```
