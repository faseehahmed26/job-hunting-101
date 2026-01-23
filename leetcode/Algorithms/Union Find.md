# Union-Find Algorithm: An Overview

Union-Find (also called Disjoint Set Union or DSU) is a data structure that efficiently tracks and merges disjoint sets. It's used to solve connectivity problems in graphs where you need to group elements into sets and check if elements belong to the same set.

---

## Key Concepts

### Core Operations

- **Find**: Determine which set an element belongs to (find its root).
- **Union**: Merge two sets into one.

### Optimizations

- **Path Compression**: Make all nodes point directly to root during Find.
- **Union by Rank/Size**: Attach smaller tree under larger tree during Union.

### Time Complexity

- Both Find and Union: O(α(n)) ≈ O(1) where α is inverse Ackermann (nearly constant)

---

## Basic Implementation

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))  # Each node is its own parent initially
        self.rank = [0] * n           # Track tree heights
    
    def find(self, x):
        # Path compression: make x point directly to root
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False  # Already in same set
        
        # Union by rank: attach shorter tree under taller tree
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        
        return True  # Union was successful
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

---

## When to Use Union-Find

Use Union-Find when you need to:
- Track connected components in a graph
- Detect cycles in undirected graphs
- Group elements into disjoint sets dynamically
- Check if two elements belong to the same group
- Implement Kruskal's MST algorithm

**Key indicators**:
- "Are X and Y connected?"
- "Group elements into sets"
- "Number of connected components"
- "Detect if adding edge creates cycle"

---

## Common Problem Patterns

## 1. Number of Connected Components

Find how many separate groups exist in a graph.

```python
def count_components(n, edges):
    uf = UnionFind(n)
    
    for u, v in edges:
        uf.union(u, v)
    
    # Count unique roots
    return len(set(uf.find(i) for i in range(n)))
```

---

## 2. Detect Cycle in Undirected Graph

Check if adding an edge creates a cycle.

```python
def has_cycle(n, edges):
    uf = UnionFind(n)
    
    for u, v in edges:
        if not uf.union(u, v):  # If already connected
            return True  # Cycle detected
    
    return False
```

---

## 3. Friend Circles / Provinces

Group people into friend circles.

```python
def find_circle_num(is_connected):
    n = len(is_connected)
    uf = UnionFind(n)
    
    for i in range(n):
        for j in range(i + 1, n):
            if is_connected[i][j] == 1:
                uf.union(i, j)
    
    return len(set(uf.find(i) for i in range(n)))
```

---

## 4. Accounts Merge

Merge accounts that share common emails.

```python
def accounts_merge(accounts):
    uf = UnionFind(len(accounts))
    email_to_id = {}
    
    # Map emails to account IDs
    for i, account in enumerate(accounts):
        for email in account[1:]:
            if email in email_to_id:
                uf.union(i, email_to_id[email])
            else:
                email_to_id[email] = i
    
    # Group emails by root account
    from collections import defaultdict
    merged = defaultdict(list)
    
    for email, acc_id in email_to_id.items():
        root = uf.find(acc_id)
        merged[root].append(email)
    
    # Format result
    result = []
    for acc_id, emails in merged.items():
        result.append([accounts[acc_id][0]] + sorted(emails))
    
    return result
```

---

## 5. Redundant Connection

Find the edge that creates a cycle.

```python
def find_redundant_connection(edges):
    uf = UnionFind(len(edges) + 1)
    
    for u, v in edges:
        if not uf.union(u, v):
            return [u, v]
    
    return []
```

---

## 6. Earliest Friends (Timestamp Based)

Find earliest time when all people become connected.

```python
def earliest_acq(logs, n):
    logs.sort()  # Sort by timestamp
    uf = UnionFind(n)
    components = n
    
    for timestamp, u, v in logs:
        if uf.union(u, v):
            components -= 1
            if components == 1:
                return timestamp
    
    return -1  # Not all connected
```

---

## 7. Number of Islands (Union-Find Approach)

Count connected land masses in a grid.

```python
def num_islands(grid):
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    uf = UnionFind(rows * cols)
    
    def index(r, c):
        return r * cols + c
    
    water_count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '0':
                water_count += 1
                continue
            
            # Union with adjacent land cells
            for dr, dc in [(0, 1), (1, 0)]:
                nr, nc = r + dr, c + dc
                if nr < rows and nc < cols and grid[nr][nc] == '1':
                    uf.union(index(r, c), index(nr, nc))
    
    # Total cells - water - duplicate land roots
    land_roots = set()
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                land_roots.add(uf.find(index(r, c)))
    
    return len(land_roots)
```

---

## Union-Find with Size Tracking

Sometimes you need to track the size of each set.

```python
class UnionFindWithSize:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n  # Track size instead of rank
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False
        
        # Attach smaller to larger
        if self.size[root_x] < self.size[root_y]:
            self.parent[root_x] = root_y
            self.size[root_y] += self.size[root_x]
        else:
            self.parent[root_y] = root_x
            self.size[root_x] += self.size[root_y]
        
        return True
    
    def get_size(self, x):
        return self.size[self.find(x)]
```

---

## Tips

- Always use both path compression and union by rank/size for optimal performance
- Union returns boolean: True if sets were merged, False if already connected
- For grid problems, convert 2D coordinates to 1D index: `index = row * cols + col`
- To count components, count unique roots: `len(set(uf.find(i) for i in range(n)))`
- Track component count manually by decrementing when successful union occurs
- Union-Find only works for undirected graphs (for directed, use different approaches)

---

## Advantages

- Nearly O(1) operations with optimizations
- Simple to implement
- Efficient for dynamic connectivity
- Natural fit for many graph problems
- No need for explicit graph representation

---

By mastering Union-Find, you'll efficiently solve connectivity, grouping, and cycle detection problems in interviews.