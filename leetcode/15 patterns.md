# 15 Essential LeetCode Patterns - Template Code & Intuition

This guide covers 15 essential patterns for solving LeetCode problems, with template code and key insights for each pattern.

---

## 1. Prefix Sum

**When to Use:** Multiple queries on subarray sums, cumulative calculations

**Intuition:** If asking "sum between indices" repeatedly → precompute prefix sums

**Time Complexity:** O(n) preprocessing, O(1) per query

```python
def prefix_sum_template(nums):
    n = len(nums)
    prefix = [0] * (n + 1)
    
    # Build prefix sum array
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    # Query sum from index i to j (inclusive)
    def range_sum(i, j):
        return prefix[j + 1] - prefix[i]
    
    return range_sum

# Example: nums = [1,2,3,4,5], range_sum(1,3) = 2+3+4 = 9
```

---

## 2. Two Pointers

**When to Use:** Sorted array, finding pairs/triplets, removing duplicates

**Intuition:** Need to check pairs? Start from both ends, move based on comparison

**Time Complexity:** O(n)

```python
def two_pointers_template(nums, target):
    left, right = 0, len(nums) - 1
    
    while left < right:
        curr_sum = nums[left] + nums[right]
        
        if curr_sum == target:
            return [left, right]
        elif curr_sum < target:
            left += 1  # Need larger sum
        else:
            right -= 1  # Need smaller sum
    
    return [-1, -1]

# VARIATION: Remove duplicates in sorted array
def remove_duplicates(nums):
    if not nums:
        return 0
    
    write = 1
    for read in range(1, len(nums)):
        if nums[read] != nums[read - 1]:
            nums[write] = nums[read]
            write += 1
    
    return write
```

---

## 3. Sliding Window

**When to Use:** Contiguous subarray/substring, "find max/min in window of size k"

**Intuition:** Keywords: "subarray", "substring", "consecutive", "window size k"

**Time Complexity:** O(n)

### Fixed Size Window

```python
def sliding_window_fixed(nums, k):
    window_sum = sum(nums[:k])
    max_sum = window_sum
    
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    
    return max_sum
```

### Variable Size Window

```python
def sliding_window_variable(s, target):
    left = 0
    curr_sum = 0
    max_len = 0
    
    for right in range(len(s)):
        curr_sum += s[right]
        
        # Shrink window if needed
        while curr_sum > target and left <= right:
            curr_sum -= s[left]
            left += 1
        
        max_len = max(max_len, right - left + 1)
    
    return max_len
```

---

## 4. Fast & Slow Pointers (Floyd's Cycle Detection)

**When to Use:** Detect cycle in linked list, find middle element

**Intuition:** Two speeds → they'll meet if there's a cycle

**Time Complexity:** O(n)

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def has_cycle(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False

def find_cycle_start(head):
    slow = fast = head
    
    # Find meeting point
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    
    if not fast or not fast.next:
        return None
    
    # Find start of cycle
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```

---

## 5. Linked List In-Place Reversal

**When to Use:** Reverse linked list or part of it

**Intuition:** "Reverse" + "linked list" → adjust pointers in-place

**Time Complexity:** O(n)  
**Space Complexity:** O(1)

```python
def reverse_linked_list(head):
    prev = None
    curr = head
    
    while curr:
        next_temp = curr.next
        curr.next = prev
        prev = curr
        curr = next_temp
    
    return prev

def reverse_between(head, left, right):
    if not head or left == right:
        return head
    
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    
    # Move to position before 'left'
    for _ in range(left - 1):
        prev = prev.next
    
    # Reverse from left to right
    curr = prev.next
    for _ in range(right - left):
        temp = curr.next
        curr.next = temp.next
        temp.next = prev.next
        prev.next = temp
    
    return dummy.next
```

---

## 6. Monotonic Stack

**When to Use:** "Next greater/smaller element", histogram problems

**Intuition:** Need to find next larger/smaller? → Stack maintains order

**Time Complexity:** O(n)

### Next Greater Element

```python
def next_greater_element(nums):
    result = [-1] * len(nums)
    stack = []  # Store indices
    
    for i in range(len(nums)):
        # Pop smaller elements and set their next greater
        while stack and nums[stack[-1]] < nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]
        
        stack.append(i)
    
    return result
```

### Next Smaller Element

```python
def next_smaller_element(nums):
    result = [-1] * len(nums)
    stack = []
    
    for i in range(len(nums)):
        while stack and nums[stack[-1]] > nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]
        
        stack.append(i)
    
    return result
```

---

## 7. Top K Elements

**When to Use:** "K largest/smallest", "K most frequent"

**Intuition:** Don't sort everything → use heap of size K

**Time Complexity:** O(n log k)

```python
import heapq

def find_kth_largest(nums, k):
    # Min heap of size k
    heap = nums[:k]
    heapq.heapify(heap)
    
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    
    return heap[0]

def top_k_frequent(nums, k):
    from collections import Counter
    count = Counter(nums)
    
    # Max heap (negate for Python's min heap)
    return heapq.nlargest(k, count.keys(), key=count.get)
```

---

## 8. Overlapping Intervals

**When to Use:** Merge intervals, scheduling, overlapping ranges

**Intuition:** Sort by start time, check if current overlaps with previous

**Time Complexity:** O(n log n)

```python
def merge_intervals(intervals):
    if not intervals:
        return []
    
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    
    for current in intervals[1:]:
        last = merged[-1]
        
        # Check overlap: last[1] >= current[0]
        if last[1] >= current[0]:
            merged[-1] = [last[0], max(last[1], current[1])]
        else:
            merged.append(current)
    
    return merged

def insert_interval(intervals, new_interval):
    res=[]
        for i, (s,e) in enumerate(intervals):
            if newInterval[0]>e:
                res.append([s,e])
            elif newInterval[1]<s:
                res.append(newInterval)
                return res+intervals[i:]
            else:
                newInterval[0]=min(newInterval[0],s)
                newInterval[1]=max(newInterval[1],e)
        res.append(newInterval)
        return res
```

---

## 9. Modified Binary Search

**When to Use:** Sorted/rotated array, search in sorted matrix

**Intuition:** Array has some order → can eliminate half each time

**Time Complexity:** O(log n)

### Standard Binary Search

```python
def binary_search_template(nums, target):
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1
```

### Search in Rotated Array

```python
def search_rotated_array(nums, target):
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if nums[mid] == target:
            return mid
        
        # Check which half is sorted
        if nums[left] <= nums[mid]:  # Left half sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:  # Right half sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1
```

---

## 10. Binary Tree Traversal

**When to Use:** Tree problems requiring visiting all nodes

**Intuition:** PreOrder=root first, InOrder=root middle, PostOrder=root last

**Time Complexity:** O(n)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def preorder_traversal(root):
    # Root -> Left -> Right
    result = []
    
    def dfs(node):
        if not node:
            return
        result.append(node.val)
        dfs(node.left)
        dfs(node.right)
    
    dfs(root)
    return result

def inorder_traversal(root):
    # Left -> Root -> Right (sorted for BST)
    result = []
    
    def dfs(node):
        if not node:
            return
        dfs(node.left)
        result.append(node.val)
        dfs(node.right)
    
    dfs(root)
    return result

def postorder_traversal(root):
    # Left -> Right -> Root
    result = []
    
    def dfs(node):
        if not node:
            return
        dfs(node.left)
        dfs(node.right)
        result.append(node.val)
    
    dfs(root)
    return result
```

---

## 11. Depth-First Search (DFS)

**When to Use:** Explore all paths, connected components, backtracking

**Intuition:** Need to explore deeply? → DFS (recursion or stack)

**Time Complexity:** O(V + E) for graphs

### Graph DFS

```python
def dfs_graph_template(graph, start):
    visited = set()
    result = []
    
    def dfs(node):
        if node in visited:
            return
        
        visited.add(node)
        result.append(node)
        
        for neighbor in graph[node]:
            dfs(neighbor)
    
    dfs(start)
    return result
```

### Tree Paths

```python
def dfs_tree_paths(root):
    # Find all root-to-leaf paths
    paths = []
    
    def dfs(node, path):
        if not node:
            return
        
        path.append(node.val)
        
        if not node.left and not node.right:
            paths.append(path[:])
        
        dfs(node.left, path)
        dfs(node.right, path)
        path.pop()
    
    dfs(root, [])
    return paths
```

---

## 12. Breadth-First Search (BFS)

**When to Use:** Shortest path, level-order traversal, minimum steps

**Intuition:** Need shortest/level-by-level? → BFS (queue)

**Time Complexity:** O(V + E)

```python
from collections import deque

def bfs_graph_template(graph, start):
    visited = set([start])
    queue = deque([start])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result

def level_order_traversal(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level)
    
    return result
```

---

## 13. Matrix Traversal

**When to Use:** 2D grid problems, islands, flood fill

**Intuition:** Grid + neighbors → DFS/BFS in 4 directions

**Time Complexity:** O(m × n)

### Matrix DFS Template

```python
def matrix_dfs_template(matrix):
    if not matrix:
        return
    
    rows, cols = len(matrix), len(matrix[0])
    visited = set()
    
    def dfs(r, c):
        if (r < 0 or r >= rows or c < 0 or c >= cols or
            (r, c) in visited or matrix[r][c] == 0):
            return
        
        visited.add((r, c))
        
        # Visit 4 directions
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    # Start DFS from each unvisited cell
    for r in range(rows):
        for c in range(cols):
            if matrix[r][c] == 1 and (r, c) not in visited:
                dfs(r, c)
```

### Flood Fill

```python
def flood_fill(image, sr, sc, new_color):
    if image[sr][sc] == new_color:
        return image
    
    old_color = image[sr][sc]
    rows, cols = len(image), len(image[0])
    
    def dfs(r, c):
        if (r < 0 or r >= rows or c < 0 or c >= cols or
            image[r][c] != old_color):
            return
        
        image[r][c] = new_color
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    dfs(sr, sc)
    return image
```

---

## 14. Backtracking

**When to Use:** Generate all combinations, permutations, subsets

**Intuition:** "All possible" solutions → try all, backtrack on failure

**Time Complexity:** Exponential (but often necessary)

### Backtracking Template

```python
def backtrack_template(nums):
    result = []
    
    def backtrack(path, start):
        # Base case: add current path to result
        result.append(path[:])
        
        for i in range(start, len(nums)):
            # Choose
            path.append(nums[i])
            
            # Explore
            backtrack(path, i + 1)
            
            # Unchoose (backtrack)
            path.pop()
    
    backtrack([], 0)
    return result
```

### Permutations

```python
def permutations(nums):
    result = []
    
    def backtrack(path):
        if len(path) == len(nums):
            result.append(path[:])
            return
        
        for num in nums:
            if num in path:
                continue
            path.append(num)
            backtrack(path)
            path.pop()
    
    backtrack([])
    return result
```

### Combinations

```python
def combinations(n, k):
    result = []
    
    def backtrack(start, path):
        if len(path) == k:
            result.append(path[:])
            return
        
        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(1, [])
    return result
```

---

## 15. Dynamic Programming

**When to Use:** Overlapping subproblems, optimal substructure

**Intuition:** Can break into smaller identical problems? → DP

**Time Complexity:** Usually O(n²) or O(n)

### Pattern 1: Fibonacci (1D DP)

```python
def fibonacci(n):
    if n <= 1:
        return n
    
    dp = [0] * (n + 1)
    dp[0], dp[1] = 0, 1
    
    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]
    
    return dp[n]
```

### Pattern 2: 0/1 Knapsack

```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            if weights[i - 1] <= w:
                dp[i][w] = max(
                    values[i - 1] + dp[i - 1][w - weights[i - 1]],
                    dp[i - 1][w]
                )
            else:
                dp[i][w] = dp[i - 1][w]
    
    return dp[n][capacity]
```

### Pattern 3: Longest Common Subsequence

```python
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    return dp[m][n]
```

### Pattern 4: Longest Increasing Subsequence

```python
def lis(nums):
    if not nums:
        return 0
    
    dp = [1] * len(nums)
    
    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)
```

---

## Pattern Selection Cheat Sheet

Use this quick reference to identify which pattern to use based on problem keywords:

| Keywords | Pattern |
|----------|---------|
| "subarray sum", "range query" | Prefix Sum |
| "sorted array", "pair sum" | Two Pointers |
| "substring", "window size k" | Sliding Window |
| "cycle in linked list" | Fast & Slow Pointers |
| "reverse linked list" | In-place Reversal |
| "next greater/smaller" | Monotonic Stack |
| "top k", "kth largest" | Heap |
| "merge intervals", "overlap" | Interval Sorting |
| "rotated sorted array" | Modified Binary Search |
| "tree traversal" | DFS/BFS |
| "shortest path", "level order" | BFS |
| "2D grid", "islands" | Matrix Traversal |
| "all combinations", "generate all" | Backtracking |
| "minimum/maximum", "count ways" | Dynamic Programming |

---

This guide provides template code and intuition for the 15 most essential LeetCode patterns. Practice recognizing these patterns in problems to improve your problem-solving efficiency!
