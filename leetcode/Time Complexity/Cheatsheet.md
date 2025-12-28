# Interview Complexity Cheatsheet

This cheatsheet summarizes the time and space complexity of common Python data structures and core algorithms, with emphasis on patterns frequently tested in Meta interviews.

---

## Python Built-in Data Structures

### Lists (Dynamic Arrays)

| Operation | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| `append()` | O(1) | O(1) | O(1) | Amortized constant time |
| `pop()` (last) | O(1) | O(1) | O(1) | Remove from end |
| `pop(i)` | O(n) | O(n) | O(1) | Worst when `i = 0` |
| `insert(i, x)` | O(n) | O(n) | O(1) | Elements after `i` shift |
| `remove(x)` | O(n) | O(n) | O(1) | Search then remove |
| `index(x)` | O(n) | O(n) | O(1) | Linear search |
| `count(x)` | O(n) | O(n) | O(1) | Full scan |
| `sort()` | O(n log n) | O(n log n) | O(n) | Timsort |
| `reverse()` | O(n) | O(n) | O(1) | In-place |
| `copy()` | O(n) | O(n) | O(n) | New list |
| `clear()` | O(n) | O(n) | O(1) | Deallocates elements |
| List comprehension | O(n) | O(n) | O(n) | Loop plus append |

---

### Dictionaries (Hash Tables)

| Operation | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| `get(key)` | O(1) | O(n) | O(1) | Collisions cause worst case |
| `d[key] = value` | O(1) | O(n) | O(1) | Insert or update |
| `del d[key]` | O(1) | O(n) | O(1) | Delete |
| `keys()` | O(n) | O(n) | O(n) | View object |
| `values()` | O(n) | O(n) | O(n) | View object |
| `items()` | O(n) | O(n) | O(n) | View object |
| `pop(key)` | O(1) | O(n) | O(1) | Remove and return |
| `clear()` | O(n) | O(n) | O(1) | Remove all |
| `copy()` | O(n) | O(n) | O(n) | Shallow copy |
| `update(other)` | O(k) | O(k · n) | O(k) | `k = len(other)` |

---

### Sets (Hash Tables)

| Operation | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| `add(x)` | O(1) | O(n) | O(1) | Hash collisions |
| `remove(x)` | O(1) | O(n) | O(1) | Raises `KeyError` |
| `discard(x)` | O(1) | O(n) | O(1) | No error |
| `pop()` | O(1) | O(1) | O(1) | Arbitrary element |
| `union(other)` | O(n + m) | O(n · m) | O(n + m) | New set |
| `intersection(other)` | O(min(n, m)) | O(n · m) | O(min(n, m)) | New set |
| `difference(other)` | O(n) | O(n · m) | O(n) | `s - other` |
| `symmetric_difference(other)` | O(n + m) | O(n · m) | O(n + m) | New set |

---

### Deque (`collections.deque`)

| Operation | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| `append(x)` | O(1) | O(1) | O(1) | Right end |
| `appendleft(x)` | O(1) | O(1) | O(1) | Left end |
| `pop()` | O(1) | O(1) | O(1) | Right end |
| `popleft()` | O(1) | O(1) | O(1) | Left end |
| `remove(x)` | O(n) | O(n) | O(1) | Linear search |

---

### Heapq (Binary Min-Heap)

| Operation | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| `heappush(heap, x)` | O(log n) | O(log n) | O(1) | Maintain heap |
| `heappop(heap)` | O(log n) | O(log n) | O(1) | Remove min |
| `heapify(list)` | O(n) | O(n) | O(1) | In-place |
| `nlargest(k, it)` | O(n log k) | O(n log k) | O(k) | Top k |
| `nsmallest(k, it)` | O(n log k) | O(n log k) | O(k) | Bottom k |

---

### Strings (Immutable Sequences)

| Operation | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| Concatenation (`s1 + s2`) | O(n + m) | O(n + m) | O(n + m) | New string |
| Slicing (`s[i:j]`) | O(k) | O(k) | O(k) | `k = j - i` |
| Indexing (`s[i]`) | O(1) | O(1) | O(1) | Direct access |
| `find(sub)` | O(n · m) | O(n · m) | O(1) | Naive search |
| `replace(old, new)` | O(n · m) | O(n · m) | O(n) | New string |
| `split(delim)` | O(n) | O(n) | O(n) | List output |
| `join(iterable)` | O(n) | O(n) | O(n) | Total length |

---

## Sorting Algorithms

| Algorithm | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| Quick Sort | O(n log n) | O(n²) | O(log n) | Bad pivots |
| Merge Sort | O(n log n) | O(n log n) | O(n) | Stable |
| Heap Sort | O(n log n) | O(n log n) | O(1) | Not stable |
| Tim Sort (Python) | O(n log n) | O(n log n) | O(n) | Best case O(n) |
| Insertion Sort | O(n²) | O(n²) | O(1) | Best case O(n) |
| Selection Sort | O(n²) | O(n²) | O(1) | Always quadratic |
| Bubble Sort | O(n²) | O(n²) | O(1) | Best case O(n) |

---

## Searching Algorithms

| Algorithm | Average Case | Worst Case | Space | Notes |
|---------|--------------|------------|-------|------|
| Binary Search | O(log n) | O(log n) | O(1) | Sorted array |
| Linear Search | O(n) | O(n) | O(1) | Unsorted |

---

## Graph Algorithms (Critical for Meta)

| Algorithm | Time | Space | Notes |
|---------|------|-------|------|
| BFS | O(V + E) | O(V) | Queue, unweighted shortest path |
| DFS | O(V + E) | O(V) | Stack or recursion |
| Dijkstra | O((V + E) log V) | O(V) | Non-negative weights |
| Bellman-Ford | O(V · E) | O(V) | Negative weights |
| Floyd-Warshall | O(V³) | O(V²) | All-pairs |
| Kruskal MST | O(E log E) | O(V) | Union-Find |
| Prim MST | O((V + E) log V) | O(V) | Priority queue |
| Topological Sort | O(V + E) | O(V) | DAG only |

---

## Tree Algorithms

| Algorithm | Time | Space | Notes |
|---------|------|-------|------|
| Traversals (In, Pre, Post) | O(n) | O(h) | `h = height` |
| BST Search | O(log n) avg, O(n) worst | O(1) | Unbalanced worst |
| BST Insert | O(log n) avg, O(n) worst | O(1) | Unbalanced worst |
| BST Delete | O(log n) avg, O(n) worst | O(1) | Unbalanced worst |

---

## Meta-Specific Priority Areas

**Most Frequently Tested**
- Arrays and strings
- Hash tables
- Two pointers
- Sliding window

**Critical for Social Graph Problems**
- BFS and DFS
- Bidirectional BFS
- Tree traversals

**Important Notes**
- Dynamic programming is typically not expected.
- Time pressure is high, around 17 to 18 minutes per problem.
- Always discuss time and space complexity.
- Clear communication while coding is essential.

---

## Common Complexity Patterns

| Pattern | Time | Space | Examples |
|-------|------|-------|---------|
| Single loop | O(n) | O(1) | Sum, linear scan |
| Nested loops | O(n²) | O(1) | Brute force |
| Divide and conquer | O(n log n) | O(log n) | Binary search |
| Hash lookup | O(1) avg | O(n) | Two sum |
| Tree ops | O(log n) | O(log n) | Balanced BST |
| Graph traversal | O(V + E) | O(V) | BFS, DFS |

---

## When Worst Case Occurs

- Lists: `pop(0)` and `insert(0, x)` are O(n)
- Hash structures: Poor hashing or many collisions
- Quick sort: Already sorted input
- BST: Unbalanced tree
- Graphs: Dense graphs with many edges

---

This cheatsheet covers the most important data structures and algorithms for Meta technical interviews, with emphasis on patterns and complexities most frequently tested.
