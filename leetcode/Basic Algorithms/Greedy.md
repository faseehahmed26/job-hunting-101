````md
https://leetcode.com/tag/greedy/

https://leetcode.com/articles/greedy-algorithms/

https://www.geeksforgeeks.org/greedy-algorithms/

# Greedy Algorithm: Complete Interview Guide

Greedy algorithms solve problems by making the **locally optimal choice at each step**, with the hope that these choices lead to a **globally optimal solution**. They do **not backtrack** and do not explore all possibilities.

---

## 1. Pattern Identification  
### How to Know When to Use Greedy

### Key Signals in the Problem Statement
- You are asked to:
  - Maximize or minimize something
  - Choose the “best” option at each step
- Keywords like:
  - “maximum”
  - “minimum”
  - “earliest”
  - “latest”
  - “shortest”
  - “highest profit”
- Constraints suggest:
  - Sorting helps
  - One-pass decisions after sorting

### Core Question to Ask
> If I make the best choice **right now**, can I still reach an optimal solution later?

If yes, greedy likely applies.

---

## 2. Core Properties of Greedy Algorithms

### Greedy Choice Property
A globally optimal solution can be reached by making a locally optimal choice at each step.

### Optimal Substructure
An optimal solution to the problem contains optimal solutions to its subproblems.

### No Backtracking
Once a decision is made, it is never reconsidered.

---

## 3. General Greedy Framework

```python
def greedy_problem(input):
    # 1. Sort input by greedy criterion
    input.sort(key=greedy_key)

    result = initial_state

    # 2. Iterate
    for item in input:
        # 3. Make greedy decision
        if is_valid_choice(item, result):
            update_result(item, result)

    return result
````

---

## 4. Essential Greedy Patterns (With Examples)

---

### Pattern 1: Activity / Interval Selection

**Problem**
Select the maximum number of non-overlapping intervals.

**Greedy Choice**
Always pick the interval that ends earliest.

```python
def max_activities(intervals):
    intervals.sort(key=lambda x: x[1])
    count = 0
    last_end = float('-inf')

    for start, end in intervals:
        if start >= last_end:
            count += 1
            last_end = end

    return count
```

**Time Complexity**: O(n log n)

---

### Pattern 2: Fractional Knapsack

**Problem**
Maximize value with weight constraint, fractions allowed.

**Greedy Choice**
Pick item with highest value-to-weight ratio.

```python
def fractional_knapsack(items, capacity):
    items.sort(key=lambda x: x[1] / x[0], reverse=True)
    total_value = 0

    for weight, value in items:
        if capacity >= weight:
            total_value += value
            capacity -= weight
        else:
            total_value += value * (capacity / weight)
            break

    return total_value
```

**Why Greedy Works**
Fractions allow optimal local decisions.

---

### Pattern 3: Huffman Encoding

**Problem**
Build optimal prefix-free codes based on frequencies.

**Greedy Choice**
Always merge the two least frequent nodes.

```python
import heapq

def huffman_encoding(freq):
    heap = [[w, [c, ""]] for c, w in freq.items()]
    heapq.heapify(heap)

    while len(heap) > 1:
        lo = heapq.heappop(heap)
        hi = heapq.heappop(heap)

        for p in lo[1:]:
            p[1] = '0' + p[1]
        for p in hi[1:]:
            p[1] = '1' + p[1]

        heapq.heappush(heap, [lo[0] + hi[0]] + lo[1:] + hi[1:])

    return heap[0][1:]
```

**Data Structure**: Min-heap
**Time Complexity**: O(n log n)

---

### Pattern 4: Minimum Spanning Tree (MST)

#### Kruskal’s Algorithm

**Greedy Choice**
Pick the smallest edge that does not form a cycle.

```python
def kruskal(edges, n):
    edges.sort(key=lambda x: x[2])
    parent = list(range(n))

    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])
        return parent[x]

    def union(a, b):
        parent[find(b)] = find(a)

    mst = []
    for u, v, w in edges:
        if find(u) != find(v):
            union(u, v)
            mst.append((u, v, w))

    return mst
```

**Key Data Structure**: Union-Find

---

### Pattern 5: Dijkstra’s Shortest Path

**Problem**
Shortest paths with non-negative weights.

**Greedy Choice**
Always expand the node with the smallest known distance.

```python
import heapq

def dijkstra(graph, start):
    dist = {v: float('inf') for v in graph}
    dist[start] = 0
    heap = [(0, start)]

    while heap:
        d, u = heapq.heappop(heap)
        if d > dist[u]:
            continue

        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                heapq.heappush(heap, (dist[v], v))

    return dist
```

---

### Pattern 6: Job Sequencing with Deadlines

**Problem**
Maximize profit with deadlines.

**Greedy Choice**
Schedule jobs by highest profit first.

```python
def job_sequencing(jobs):
    jobs.sort(key=lambda x: x[1], reverse=True)
    max_deadline = max(d for d, _ in jobs)
    slots = [-1] * (max_deadline + 1)
    profit = 0

    for d, p in jobs:
        for t in range(d, 0, -1):
            if slots[t] == -1:
                slots[t] = p
                profit += p
                break

    return profit
```

---

### Pattern 7: Greedy String Problems

**Example**
Rearrange string so no two adjacent characters are same.

```python
import heapq
from collections import Counter

def reorganize_string(s):
    freq = Counter(s)
    heap = [(-c, ch) for ch, c in freq.items()]
    heapq.heapify(heap)

    prev = (0, '')
    res = []

    while heap:
        c, ch = heapq.heappop(heap)
        res.append(ch)

        if prev[0] < 0:
            heapq.heappush(heap, prev)

        prev = (c + 1, ch)

    return "".join(res) if len(res) == len(s) else ""
```

---

## 5. Common Greedy Structures

| Pattern             | Data Structure |
| ------------------- | -------------- |
| Interval selection  | Sorting        |
| Scheduling          | Heap           |
| MST                 | Union-Find     |
| Shortest path       | Priority Queue |
| String reordering   | Heap           |
| Resource allocation | Heap           |

---

## 6. Greedy vs Dynamic Programming

| Greedy                   | DP                 |
| ------------------------ | ------------------ |
| Local choice             | Global exploration |
| Fast                     | Slower             |
| No backtracking          | Full state         |
| Requires greedy property | Always correct     |

If greedy choice property fails, switch to DP.

---

## 7. Common Pitfalls

* Assuming greedy always works
* Skipping proof of correctness
* Forgetting to sort
* Mishandling equal values
* Ignoring edge cases

---

## 8. Interview Identification Checklist

Before coding, ask:

* Can input be sorted meaningfully?
* Can I make a local decision safely?
* Does choosing earliest / smallest / largest help?
* Can later decisions compensate for earlier ones?

If yes, greedy applies.

---

## 9. Time & Space Complexity

* Sorting: O(n log n)
* Heap operations: O(log n)
* Traversal: O(n)
* Space: Usually O(n)

---

## 10. Practice Problems

### Easy

* Assign Cookies (455)
* Lemonade Change (860)

### Medium

* Jump Game (55)
* Gas Station (134)
* Task Scheduler (621)

### Hard

* Job Scheduling (1235)
* Minimum Cost to Hire Workers (857)

---

## Final Insight

Greedy algorithms work **only when local optimal choices lead to global optimality**.

In interviews, always:

1. Explain why greedy works
2. Show the greedy choice
3. Prove correctness briefly
4. Code cleanly

Mastering greedy unlocks **intervals, heaps, graphs, scheduling, and optimization problems**.

```
```
