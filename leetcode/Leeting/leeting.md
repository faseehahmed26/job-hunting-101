````md
# Interview Algorithm Templates (Python)

This document is a **clean, interview-ready template collection** for common algorithmic patterns. Use these as starting points during live coding.

---

## Two Pointers

### One Input, Opposite Ends
```python
def fn(arr):
    left, right = 0, len(arr) - 1
    ans = 0

    while left < right:
        if CONDITION:
            left += 1
        else:
            right -= 1

    return ans
````

### Two Inputs, Exhaust Both

```python
def fn(arr1, arr2):
    i = j = ans = 0

    while i < len(arr1) and j < len(arr2):
        if CONDITION:
            i += 1
        else:
            j += 1

    while i < len(arr1):
        i += 1

    while j < len(arr2):
        j += 1

    return ans
```

---

## Sliding Window

```python
def fn(arr):
    left = 0
    ans = 0
    curr = 0

    for right in range(len(arr)):
        # expand window using arr[right]

        while WINDOW_CONDITION_BROKEN:
            # shrink window from left
            left += 1

    return ans
```

---

## Prefix Sum

```python
def fn(arr):
    prefix = [arr[0]]
    for i in range(1, len(arr)):
        prefix.append(prefix[-1] + arr[i])
    return prefix
```

---

## Efficient String Building

```python
def fn(arr):
    res = []
    for c in arr:
        res.append(c)
    return "".join(res)
```

---

## Linked List Patterns

### Fast and Slow Pointer

```python
def fn(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

### Reverse Linked List

```python
def fn(head):
    prev = None
    curr = head

    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt

    return prev
```

---

## Subarrays with Exact Criteria (Prefix + Hash Map)

```python
from collections import defaultdict

def fn(arr, k):
    counts = defaultdict(int)
    counts[0] = 1
    curr = ans = 0

    for num in arr:
        # update curr
        ans += counts[curr - k]
        counts[curr] += 1

    return ans
```

---

## Monotonic Stack (Increasing)

```python
def fn(arr):
    stack = []
    for num in arr:
        while stack and stack[-1] > num:
            stack.pop()
        stack.append(num)
    return stack
```

---

## Binary Tree

### DFS (Recursive)

```python
def dfs(root):
    if not root:
        return 0
    left = dfs(root.left)
    right = dfs(root.right)
    return left + right
```

### DFS (Iterative)

```python
def dfs(root):
    stack = [root]
    ans = 0

    while stack:
        node = stack.pop()
        # process node
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)

    return ans
```

### BFS (Level Order)

```python
from collections import deque

def fn(root):
    queue = deque([root])
    ans = 0

    while queue:
        size = len(queue)
        for _ in range(size):
            node = queue.popleft()
            # process node
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

    return ans
```

---

## Graph Traversal

### DFS (Recursive)

```python
def fn(graph, start):
    seen = {start}

    def dfs(node):
        ans = 0
        for nei in graph[node]:
            if nei not in seen:
                seen.add(nei)
                ans += dfs(nei)
        return ans

    return dfs(start)
```

### DFS (Iterative)

```python
def fn(graph, start):
    stack = [start]
    seen = {start}

    while stack:
        node = stack.pop()
        for nei in graph[node]:
            if nei not in seen:
                seen.add(nei)
                stack.append(nei)
```

### BFS

```python
from collections import deque

def fn(graph, start):
    queue = deque([start])
    seen = {start}

    while queue:
        node = queue.popleft()
        for nei in graph[node]:
            if nei not in seen:
                seen.add(nei)
                queue.append(nei)
```

---

## Top K Elements (Heap)

```python
import heapq

def fn(arr, k):
    heap = []

    for num in arr:
        heapq.heappush(heap, (CRITERIA, num))
        if len(heap) > k:
            heapq.heappop(heap)

    return [num for _, num in heap]
```

---

## Binary Search

### Standard

```python
def fn(arr, target):
    l, r = 0, len(arr) - 1

    while l <= r:
        mid = (l + r) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] > target:
            r = mid - 1
        else:
            l = mid + 1

    return -1
```

### Leftmost Insertion Point

```python
def fn(arr, target):
    l, r = 0, len(arr)

    while l < r:
        mid = (l + r) // 2
        if arr[mid] >= target:
            r = mid
        else:
            l = mid + 1

    return l
```

---

## Binary Search on Answer (Greedy)

### Find Minimum

```python
def fn():
    def check(x):
        return BOOLEAN

    l, r = MIN, MAX
    while l <= r:
        mid = (l + r) // 2
        if check(mid):
            r = mid - 1
        else:
            l = mid + 1

    return l
```

### Find Maximum

```python
def fn():
    def check(x):
        return BOOLEAN

    l, r = MIN, MAX
    while l <= r:
        mid = (l + r) // 2
        if check(mid):
            l = mid + 1
        else:
            r = mid - 1

    return r
```

---

## Backtracking

```python
def backtrack(state):
    if BASE_CASE:
        return

    for choice in CHOICES:
        # apply choice
        backtrack(state)
        # undo choice
```

---

## Dynamic Programming (Top-Down)

```python
def fn():
    memo = {}

    def dp(state):
        if BASE_CASE:
            return 0
        if state in memo:
            return memo[state]

        ans = RECURRENCE(state)
        memo[state] = ans
        return ans

    return dp(INITIAL_STATE)
```

---

## Trie Construction

```python
class TrieNode:
    def __init__(self):
        self.children = {}

def build_trie(words):
    root = TrieNode()
    for word in words:
        curr = root
        for c in word:
            if c not in curr.children:
                curr.children[c] = TrieNode()
            curr = curr.children[c]
    return root
```

---

## Dijkstra’s Algorithm

```python
import heapq

def dijkstra(graph, source, n):
    dist = [float("inf")] * n
    dist[source] = 0
    heap = [(0, source)]

    while heap:
        d, node = heapq.heappop(heap)
        if d > dist[node]:
            continue

        for nei, w in graph[node]:
            nd = d + w
            if nd < dist[nei]:
                dist[nei] = nd
                heapq.heappush(heap, (nd, nei))

    return dist
```

---

## Final Note

These templates cover **90%+ of interview problems**.
Recognize the pattern first, then plug in logic.

Speed comes from **pattern recall**, not invention.
