# Kruskal's Algorithm: An Overview

Kruskal's algorithm is a greedy algorithm that finds the Minimum Spanning Tree (MST) of a weighted, undirected graph. It builds the MST by selecting edges in increasing order of weight while avoiding cycles using the Union-Find (Disjoint Set Union) data structure.

---

## Key Concepts of Kruskal's Algorithm

### Core Components

- **Edge list**: All edges sorted by weight in ascending order.
- **Union-Find (DSU)**: Data structure to track connected components and detect cycles.
- **Spanning Tree**: A subset of edges that connects all vertices with minimum total weight and no cycles.

### Algorithm Flow

1. Sort all edges by weight in ascending order.
2. Initialize Union-Find structure with each vertex as its own set.
3. Iterate through sorted edges:
   - If edge connects two different components (no cycle), add it to MST.
   - Union the two components.
4. Continue until MST has (V-1) edges or all edges are processed.

### Union-Find Operations

- **Find**: Determine which set/component a vertex belongs to.
- **Union**: Merge two sets/components into one.
- **Path Compression**: Optimization to flatten tree structure during Find.
- **Union by Rank/Size**: Attach smaller tree under larger tree root.

### Time Complexity

- **Sorting edges**: O(E log E)
- **Union-Find operations**: O(E α(V)) where α is inverse Ackermann function (nearly constant)
- **Overall**: O(E log E) or O(E log V) since E ≤ V²

---

## Types of Problems Solved Using Kruskal's Algorithm

## 1. Classic Minimum Spanning Tree

### Objective

Find the minimum cost to connect all vertices in a graph.

### Examples

- Network cable installation minimizing total cost.
- Road construction connecting cities with minimum expense.

### Approach

- Implement Union-Find data structure.
- Sort edges by weight.
- Greedily select edges that don't form cycles.

### Example: Basic Kruskal's MST

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False
        
        # Union by rank
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        
        return True

def kruskal_mst(n, edges):
    # edges: list of (weight, u, v)
    edges.sort()  # Sort by weight
    
    uf = UnionFind(n)
    mst_cost = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            mst_cost += weight
            mst_edges.append((u, v, weight))
            
            if len(mst_edges) == n - 1:
                break
    
    return mst_cost, mst_edges
```

---

## 2. Connecting Cities with Minimum Cost

### Objective

Connect all cities with minimum total construction cost.

### Examples

- LeetCode: Connecting Cities With Minimum Cost.
- Infrastructure planning problems.

### Approach

- Model cities as vertices and construction costs as edge weights.
- Apply Kruskal's algorithm directly.

### Example: Min Cost to Connect All Points

```python
def min_cost_connect_points(points):
    n = len(points)
    
    # Generate all edges with Manhattan distance
    edges = []
    for i in range(n):
        for j in range(i + 1, n):
            dist = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1])
            edges.append((dist, i, j))
    
    # Kruskal's algorithm
    edges.sort()
    uf = UnionFind(n)
    total_cost = 0
    edges_used = 0
    
    for cost, u, v in edges:
        if uf.union(u, v):
            total_cost += cost
            edges_used += 1
            
            if edges_used == n - 1:
                break
    
    return total_cost
```

---

## 3. Number of Connected Components

### Objective

Find the number of disconnected components after MST construction or during edge addition.

### Examples

- Network connectivity analysis.
- Graph partitioning problems.

### Approach

- Use Union-Find to track components.
- Count distinct root nodes after processing edges.

### Example: Count Components

```python
def count_components(n, edges):
    uf = UnionFind(n)
    
    for u, v in edges:
        uf.union(u, v)
    
    # Count unique roots
    components = len(set(uf.find(i) for i in range(n)))
    return components

def count_components_after_edges(n, edges):
    uf = UnionFind(n)
    components = n  # Initially all disconnected
    
    for u, v in edges:
        if uf.union(u, v):
            components -= 1
    
    return components
```

---

## 4. Critical Connections (Bridges)

### Objective

Find edges whose removal disconnects the graph or increases MST cost.

### Examples

- Finding critical network links.
- Identifying single points of failure.

### Approach

- Build MST using Kruskal's.
- Edges in MST that are the only connection between components are critical.
- May need additional analysis beyond basic Kruskal's.

### Example: Find Critical Edges in MST

```python
def find_critical_edges(n, edges):
    # Sort and build MST
    edges_with_idx = [(weight, u, v, i) for i, (u, v, weight) in enumerate(edges)]
    edges_with_idx.sort()
    
    uf = UnionFind(n)
    mst_edges = []
    
    for weight, u, v, idx in edges_with_idx:
        if uf.union(u, v):
            mst_edges.append((u, v, weight, idx))
    
    # An edge in MST is critical if removing it increases MST cost
    # or makes graph disconnected
    critical = []
    original_cost = sum(e[2] for e in mst_edges)
    
    for u, v, weight, idx in mst_edges:
        # Try building MST without this edge
        uf_test = UnionFind(n)
        test_cost = 0
        edges_used = 0
        
        for w, a, b, i in edges_with_idx:
            if i != idx and uf_test.union(a, b):
                test_cost += w
                edges_used += 1
        
        # If we can't form complete MST or cost increases
        if edges_used < n - 1 or test_cost > original_cost:
            critical.append(idx)
    
    return critical
```

---

## 5. Minimum Cost to Make Graph Connected

### Objective

Determine minimum edges needed to connect a disconnected graph.

### Examples

- Network repair optimization.
- Adding minimum connections to make graph fully connected.

### Approach

- Count connected components using Union-Find.
- Need (components - 1) edges to connect all.
- Check if enough extra edges are available.

### Example: Min Edges to Connect

```python
def make_connected(n, connections):
    if len(connections) < n - 1:
        return -1  # Not enough edges to connect all nodes
    
    uf = UnionFind(n)
    
    for u, v in connections:
        uf.union(u, v)
    
    # Count components
    components = len(set(uf.find(i) for i in range(n)))
    
    # Need (components - 1) edges to connect all
    return components - 1
```

---

## 6. Maximum Spanning Tree

### Objective

Find spanning tree with maximum total edge weight.

### Examples

- Maximizing network bandwidth.
- Selecting most profitable connections.

### Approach

- Sort edges in descending order (instead of ascending).
- Apply Kruskal's algorithm with reversed sorting.

### Example: Maximum Spanning Tree

```python
def maximum_spanning_tree(n, edges):
    # Sort in descending order
    edges.sort(reverse=True)
    
    uf = UnionFind(n)
    max_cost = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            max_cost += weight
            mst_edges.append((u, v, weight))
            
            if len(mst_edges) == n - 1:
                break
    
    return max_cost, mst_edges
```

---

## 7. Optimizing Water Distribution

### Objective

Minimize cost of water distribution where you can build wells or pipes.

### Examples

- LeetCode: Optimize Water Distribution in a Village.
- Resource distribution with multiple source options.

### Approach

- Add virtual source node connected to all vertices with well costs.
- Apply Kruskal's on expanded graph.

### Example: Water Distribution

```python
def min_cost_water_distribution(n, wells, pipes):
    # Create virtual node 0 for wells
    edges = []
    
    # Add edges from virtual node to each house (well costs)
    for i in range(n):
        edges.append((wells[i], 0, i + 1))
    
    # Add pipe edges
    for u, v, cost in pipes:
        edges.append((cost, u, v))
    
    # Kruskal's MST
    edges.sort()
    uf = UnionFind(n + 1)  # n houses + 1 virtual node
    total_cost = 0
    
    for cost, u, v in edges:
        if uf.union(u, v):
            total_cost += cost
    
    return total_cost
```

---

## 8. Find Redundant Connection

### Objective

Find an edge that, when removed, leaves a valid tree.

### Examples

- Cycle detection in graph construction.
- Identifying extra connection in network.

### Approach

- Process edges one by one.
- First edge that connects two already-connected nodes is redundant.

### Example: Redundant Connection

```python
def find_redundant_connection(edges):
    n = len(edges)
    uf = UnionFind(n + 1)
    
    for u, v in edges:
        if not uf.union(u, v):
            # This edge creates a cycle
            return [u, v]
    
    return []
```

---

## Tips for Solving Kruskal-Based Problems

- Always implement Union-Find with path compression and union by rank for efficiency.
- Sort edges before processing (ascending for minimum, descending for maximum).
- Remember MST has exactly (V-1) edges for V vertices.
- For cycle detection, if union returns False, edge creates a cycle.
- Consider adding virtual nodes for multi-source problems.
- Check if graph has enough edges: need at least (V-1) to form spanning tree.
- For disconnected graphs, Kruskal's finds minimum spanning forest.
- Handle edge cases: empty graph, single vertex, disconnected components.

---

## Common Variations

- **Second Best MST**: Find MST with second minimum total weight.
- **Degree-Constrained MST**: MST where vertex degrees are limited.
- **Bottleneck MST**: Minimize the maximum edge weight in spanning tree.
- **k-MST**: Find minimum spanning tree connecting exactly k vertices.

---

## Kruskal's vs Prim's Algorithm

| Aspect | Kruskal's | Prim's |
|--------|-----------|--------|
| Approach | Edge-based (global) | Vertex-based (local growth) |
| Data Structure | Union-Find | Priority Queue |
| Best for | Sparse graphs | Dense graphs |
| Time Complexity | O(E log E) | O(E log V) with binary heap |
| Edge Processing | All edges sorted first | Edges processed on-demand |

---

## Advantages of Kruskal's Algorithm

- Simple and intuitive greedy approach.
- Works well on sparse graphs.
- Easy to detect cycles using Union-Find.
- Can handle disconnected graphs (produces spanning forest).
- Naturally finds edges in order of importance.
- No need for graph representation (works with edge list).

---

## Key Pattern Recognition

Use Kruskal's algorithm when you see:
- "Minimum cost to connect all nodes/cities"
- "Minimum spanning tree"
- "Connect with minimum total weight"
- "Network construction/optimization"
- "Graph connectivity with cost constraints"
- "Remove redundant connections"

---

By mastering Kruskal's algorithm and Union-Find data structure, you will be able to solve a wide range of graph connectivity and optimization problems efficiently in interviews.