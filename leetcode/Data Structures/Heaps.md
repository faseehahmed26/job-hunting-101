# Heaps

## Heaps: An Overview

A heap is a specialized binary tree-based data structure that satisfies the **heap property**:

- **Min-Heap:** The parent node is always smaller than or equal to its children.
- **Max-Heap:** The parent node is always larger than or equal to its children.

Heaps are commonly used for priority queues, heap sort, and efficient retrieval of minimum or maximum elements.

---

## Types of Heaps

### Min-Heap
- The root node contains the smallest element.

**Example**
```

```
    2
  /   \
 3     4
/ \   / \
```

5   6 7   8

```

- Python’s `heapq` module implements a **Min-Heap** by default.

---

### Max-Heap
- The root node contains the largest element.
- Every parent node is larger than or equal to its children.

**Example**
```

```
    9
  /   \
 7     6
/ \   / \
```

5   4 3   2

```

- Python does not provide a direct Max-Heap.
- It can be simulated using a Min-Heap with **negated values**.

---

### Binary Heap
- A complete binary tree that maintains the heap property.
- Insertions and deletions take `O(log N)` time.

---

### Fibonacci Heap
- Advanced heap with amortized `O(1)` insertion and `O(log N)` extract-min.
- Used to optimize Dijkstra’s Algorithm.

---

### Binomial Heap
- A collection of binomial trees with specific properties.
- Useful for efficient merge operations.

---

## Heap Representation

Heaps are usually represented using **arrays**.

### Index Relationships
- Parent at index `i`
  - Left child: `2 * i + 1`
  - Right child: `2 * i + 2`
- Child at index `i`
  - Parent: `(i - 1) // 2`

### Example (Min-Heap as an Array)
```

Heap:   [2, 3, 4, 5, 6, 7, 8]
Index:  [0, 1, 2, 3, 4, 5, 6]

````

- The root element is always at index `0`.

---

## Heap Operations and Time Complexity

| Operation            | Min-Heap / Max-Heap | Time Complexity | Notes |
|---------------------|---------------------|-----------------|-------|
| Insert              | Yes                 | O(log N)        | Heapify up |
| Extract Min or Max  | Yes                 | O(log N)        | Heapify down |
| Get Min or Max      | Yes                 | O(1)            | Direct access |
| Heapify             | Yes                 | O(N)            | Bottom-up heapify |
| Decrease Key        | Yes                 | O(log N)        | Update and heapify |

---

## Heap Implementation in Python

### 1. Creating a Min-Heap

```python
import heapq

min_heap = []

heapq.heappush(min_heap, 3)
heapq.heappush(min_heap, 1)
heapq.heappush(min_heap, 4)
heapq.heappush(min_heap, 2)

print(min_heap)  # [1, 2, 4, 3]

min_val = heapq.heappop(min_heap)
print(min_val)   # 1
````

---

### 2. Creating a Max-Heap Using Negation

```python
import heapq

max_heap = []

heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -1)
heapq.heappush(max_heap, -4)
heapq.heappush(max_heap, -2)

print([-x for x in max_heap])  # [4, 2, 3, 1]

max_val = -heapq.heappop(max_heap)
print(max_val)  # 4
```

---

### 3. Converting a List into a Heap (Heapify)

```python
import heapq

arr = [5, 3, 8, 4, 1]
heapq.heapify(arr)
print(arr)  # [1, 3, 8, 4, 5]
```

---

## Applications of Heaps

### Priority Queue

* Used in task scheduling and event simulation.
* `heapq` provides an efficient priority queue.

```python
import heapq

tasks = []
heapq.heappush(tasks, (1, "Task A"))
heapq.heappush(tasks, (3, "Task C"))
heapq.heappush(tasks, (2, "Task B"))

print(heapq.heappop(tasks))  # (1, "Task A")
```

---

### Heap Sort

* **Time Complexity:** `O(N log N)`

**Steps**

1. Build a heap in `O(N)`
2. Extract the root `N` times in `O(N log N)`

```python
import heapq

def heap_sort(arr):
    heapq.heapify(arr)
    return [heapq.heappop(arr) for _ in range(len(arr))]

print(heap_sort([5, 1, 4, 2, 3]))  # [1, 2, 3, 4, 5]
```

---

### Dijkstra’s Algorithm

* Uses a Min-Heap for shortest path computation.
* **Time Complexity:** `O((V + E) log V)`

---

### Merging K Sorted Lists

```python
import heapq

lists = [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
merged = list(heapq.merge(*lists))
print(merged)
```

---

### Finding K Largest or Smallest Elements

```python
import heapq

nums = [7, 2, 5, 10, 8]
print(heapq.nlargest(2, nums))   # [10, 8]
print(heapq.nsmallest(2, nums))  # [2, 5]
```

---

### Streaming Data (Median Maintenance)

* Uses one Min-Heap and one Max-Heap.
* Maintains the median dynamically.

---

## Heap Problem Solving Techniques

* Choose the correct heap type:

  * Min-Heap for smallest elements
  * Max-Heap for largest elements
* Use `heapq.heapify()` for efficient initialization.
* Avoid unnecessary sorting by using `nlargest` or `nsmallest`.
* Use hybrid approaches for advanced problems such as median maintenance.

---

## Tips for Heap Problems

* Visualize the heap as a tree for clarity.
* Use priority queues when order matters.
* Use heaps for dynamic ordering problems.
* Prefer heapify over repeated insertions for better performance.

---

By mastering heap concepts, you will be well prepared to solve priority queue problems, efficient sorting tasks, and dynamic ordering challenges in real-world applications.

```
