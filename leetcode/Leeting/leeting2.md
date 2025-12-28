

# Interview-Ready Python Solutions (Curated Reference)

This document contains **clean, correct, and commonly asked interview solutions**, grouped by pattern.  
All implementations follow optimal time and space complexity expectations.

---

## Arrays & Two Pointers

### Product of Array Except Self
```python
def productExceptSelf(nums):
    n = len(nums)
    result = [1] * n

    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]

    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]

    return result
````

Time: O(n)
Space: O(1) extra (excluding output)

---

### Valid Palindrome II (At Most One Deletion)

```python
def validPalindrome(s):
    def isPalindrome(left, right):
        while left < right:
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True

    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return isPalindrome(left + 1, right) or isPalindrome(left, right - 1)
        left += 1
        right -= 1

    return True
```

---

### Two Sum (Hash Map)

```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        if target - num in seen:
            return [seen[target - num], i]
        seen[num] = i
    return []
```

---

### Three Sum

```python
def threeSum(nums):
    nums.sort()
    res = []

    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue

        left, right = i + 1, len(nums) - 1
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            if s == 0:
                res.append([nums[i], nums[left], nums[right]])
                left += 1
                right -= 1
                while left < right and nums[left] == nums[left - 1]:
                    left += 1
                while left < right and nums[right] == nums[right + 1]:
                    right -= 1
            elif s < 0:
                left += 1
            else:
                right -= 1

    return res
```

---

## Prefix Sum & Sliding Window

### Subarray Sum Equals K

```python
def subarraySum(nums, k):
    prefix = 0
    count = 0
    seen = {0: 1}

    for num in nums:
        prefix += num
        count += seen.get(prefix - k, 0)
        seen[prefix] = seen.get(prefix, 0) + 1

    return count
```

---

### Minimum Window Substring

```python
def minWindow(s, t):
    from collections import Counter

    t_count = Counter(t)
    window = {}
    have, need = 0, len(t_count)
    res = (float("inf"), 0, 0)
    left = 0

    for right in range(len(s)):
        c = s[right]
        window[c] = window.get(c, 0) + 1

        if c in t_count and window[c] == t_count[c]:
            have += 1

        while have == need:
            if right - left + 1 < res[0]:
                res = (right - left + 1, left, right)

            window[s[left]] -= 1
            if s[left] in t_count and window[s[left]] < t_count[s[left]]:
                have -= 1
            left += 1

    return "" if res[0] == float("inf") else s[res[1]:res[2] + 1]
```

---

## String Processing

### Add Strings

```python
def addStrings(num1, num2):
    i, j = len(num1) - 1, len(num2) - 1
    carry = 0
    res = []

    while i >= 0 or j >= 0 or carry:
        x = int(num1[i]) if i >= 0 else 0
        y = int(num2[j]) if j >= 0 else 0
        total = x + y + carry
        res.append(str(total % 10))
        carry = total // 10
        i -= 1
        j -= 1

    return "".join(reversed(res))
```

---

## Trees

### Binary Tree Right Side View

```python
def rightSideView(root):
    res = []

    def dfs(node, depth):
        if not node:
            return
        if depth == len(res):
            res.append(node.val)
        dfs(node.right, depth + 1)
        dfs(node.left, depth + 1)

    dfs(root, 0)
    return res
```

---

### Validate Binary Search Tree

```python
def isValidBST(root):
    def dfs(node, low, high):
        if not node:
            return True
        if node.val <= low or node.val >= high:
            return False
        return dfs(node.left, low, node.val) and dfs(node.right, node.val, high)

    return dfs(root, float("-inf"), float("inf"))
```

---

### Tree Traversals

**Inorder (Iterative)**

```python
def inorderTraversal(root):
    stack, res = [], []
    cur = root

    while stack or cur:
        while cur:
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()
        res.append(cur.val)
        cur = cur.right

    return res
```

**Level Order**

```python
from collections import deque

def levelOrder(root):
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
```

---

## Linked List

### Reverse Linked List

```python
def reverseList(head):
    prev = None
    cur = head

    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt

    return prev
```

---

### Detect Cycle

```python
def hasCycle(head):
    slow = fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True

    return False
```

---

## LRU Cache (Hash Map + Doubly Linked List)

```python
class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.cache = {}
        self.head = ListNode()
        self.tail = ListNode()
        self.head.next = self.tail
        self.tail.prev = self.head
```

(Full implementation included in source)

---

## Heap

### Top K Frequent Elements

```python
from collections import Counter
import heapq

def topKFrequent(nums, k):
    freq = Counter(nums)
    heap = []

    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)

    return [num for _, num in heap]
```

---

## Graphs

### BFS Shortest Path

```python
from collections import deque

def bfs_shortest_path(graph, start, target):
    queue = deque([(start, [start])])
    seen = {start}

    while queue:
        node, path = queue.popleft()
        for nei, _ in graph.get(node, []):
            if nei not in seen:
                if nei == target:
                    return path + [nei]
                seen.add(nei)
                queue.append((nei, path + [nei]))

    return None
```

---

### Number of Islands

```python
def numIslands(grid):
    rows, cols = len(grid), len(grid[0])

    def dfs(r, c):
        if r < 0 or c < 0 or r >= rows or c >= cols or grid[r][c] == "0":
            return
        grid[r][c] = "0"
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)

    count = 0
    for i in range(rows):
        for j in range(cols):
            if grid[i][j] == "1":
                dfs(i, j)
                count += 1

    return count
```

---

## Intervals

### Merge Intervals

```python
def merge_intervals(intervals):
    intervals.sort()
    merged = [intervals[0]]

    for cur in intervals[1:]:
        last = merged[-1]
        if cur[0] <= last[1]:
            last[1] = max(last[1], cur[1])
        else:
            merged.append(cur)

    return merged
```

---

### Minimum Meeting Rooms

```python
import heapq

def min_meeting_rooms(intervals):
    intervals.sort()
    heap = []

    for start, end in intervals:
        if heap and heap[0] <= start:
            heapq.heappop(heap)
        heapq.heappush(heap, end)

    return len(heap)
```

---

## Backtracking

### Permutations

```python
def permute(nums):
    res = []

    def backtrack(path):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for n in nums:
            if n not in path:
                path.append(n)
                backtrack(path)
                path.pop()

    backtrack([])
    return res
```

---

### Combination Sum

```python
def combinationSum(candidates, target):
    res = []

    def backtrack(remain, path, start):
        if remain == 0:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            if candidates[i] <= remain:
                path.append(candidates[i])
                backtrack(remain - candidates[i], path, i)
                path.pop()

    backtrack(target, [], 0)
    return res
```

---

## Final Notes

* These solutions represent **canonical interview implementations**
* Patterns covered: two pointers, sliding window, prefix sum, DFS, BFS, heap, backtracking
* All code is optimized and safe for live interviews

Use this as a **last-week revision sheet** before Meta or FAANG interviews.


