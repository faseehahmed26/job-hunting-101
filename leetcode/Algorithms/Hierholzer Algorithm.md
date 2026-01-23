# Hierholzer's Algorithm: An Overview

Hierholzer's algorithm finds an Eulerian path or Eulerian circuit in a graph. An Eulerian path visits every edge exactly once, while an Eulerian circuit is an Eulerian path that starts and ends at the same vertex. The algorithm uses a stack-based approach with backtracking to construct the path efficiently.

---

## Key Concepts of Hierholzer's Algorithm

### Definitions

- **Eulerian Path**: A path that visits every edge exactly once.
- **Eulerian Circuit**: An Eulerian path that starts and ends at the same vertex (a cycle).
- **Degree**: Number of edges connected to a vertex.
  - **In-degree**: Edges coming into a vertex (directed graphs).
  - **Out-degree**: Edges going out from a vertex (directed graphs).

### Existence Conditions

#### Undirected Graphs
- **Eulerian Circuit**: All vertices have even degree.
- **Eulerian Path**: Exactly 0 or 2 vertices have odd degree.

#### Directed Graphs
- **Eulerian Circuit**: Every vertex has equal in-degree and out-degree.
- **Eulerian Path**: At most one vertex has (out-degree - in-degree = 1), at most one vertex has (in-degree - out-degree = 1), all others balanced.

### Algorithm Flow

1. Check if Eulerian path/circuit exists.
2. Start from appropriate vertex (odd degree vertex for path, any vertex for circuit).
3. Use DFS with stack to traverse edges.
4. Mark edges as visited (remove from adjacency list).
5. When stuck, backtrack and add vertex to result.
6. Reverse the result to get correct order.

### Time Complexity

- **Time**: O(E) where E is number of edges.
- **Space**: O(E) for storing edges and path.

---

## Types of Problems Solved Using Hierholzer's Algorithm

## 1. Find Eulerian Circuit

### Objective

Find a path that visits every edge exactly once and returns to starting vertex.

### Examples

- Circuit board trace routing.
- Chinese Postman Problem variants.

### Approach

- Verify all vertices have even degree.
- Start DFS from any vertex.
- Use stack to build path in reverse.

### Example: Eulerian Circuit (Undirected)

```python
from collections import defaultdict

def find_eulerian_circuit(n, edges):
    graph = defaultdict(list)
    degree = defaultdict(int)
    
    # Build graph
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
        degree[u] += 1
        degree[v] += 1
    
    # Check if Eulerian circuit exists
    for v in degree:
        if degree[v] % 2 != 0:
            return []  # No Eulerian circuit
    
    # Hierholzer's algorithm
    stack = [edges[0][0]]  # Start from any vertex
    path = []
    
    while stack:
        curr = stack[-1]
        
        if graph[curr]:
            next_vertex = graph[curr].pop()
            graph[next_vertex].remove(curr)  # Remove reverse edge
            stack.append(next_vertex)
        else:
            path.append(stack.pop())
    
    return path[::-1]
```

---

## 2. Reconstruct Itinerary

### Objective

Given flight tickets, find valid itinerary visiting all tickets exactly once.

### Examples

- LeetCode: Reconstruct Itinerary.
- Travel route planning with fixed segments.

### Approach

- Build directed graph from tickets.
- Sort destinations lexicographically for smallest lexical order.
- Apply Hierholzer's from starting airport.

### Example: Reconstruct Itinerary

```python
from collections import defaultdict

def find_itinerary(tickets):
    graph = defaultdict(list)
    
    # Build graph and sort destinations
    for src, dst in tickets:
        graph[src].append(dst)
    
    for src in graph:
        graph[src].sort(reverse=True)  # Reverse sort for popping smallest
    
    stack = ['JFK']
    path = []
    
    while stack:
        curr = stack[-1]
        
        if graph[curr]:
            stack.append(graph[curr].pop())
        else:
            path.append(stack.pop())
    
    return path[::-1]
```

---

## 3. Valid Arrangement of Pairs

### Objective

Arrange pairs so that the end of one pair matches the start of the next.

### Examples

- LeetCode: Valid Arrangement of Pairs.
- Sequence reconstruction problems.

### Approach

- Build directed graph from pairs.
- Find starting vertex (out-degree - in-degree = 1).
- Apply Hierholzer's algorithm.

### Example: Valid Arrangement

```python
from collections import defaultdict

def valid_arrangement(pairs):
    graph = defaultdict(list)
    in_degree = defaultdict(int)
    out_degree = defaultdict(int)
    
    # Build graph
    for start, end in pairs:
        graph[start].append(end)
        out_degree[start] += 1
        in_degree[end] += 1
    
    # Find starting vertex
    start_vertex = pairs[0][0]
    for vertex in graph:
        if out_degree[vertex] - in_degree[vertex] == 1:
            start_vertex = vertex
            break
    
    # Hierholzer's algorithm
    stack = [start_vertex]
    path = []
    
    while stack:
        curr = stack[-1]
        
        if graph[curr]:
            stack.append(graph[curr].pop())
        else:
            path.append(stack.pop())
    
    path.reverse()
    
    # Convert path to pairs
    result = []
    for i in range(len(path) - 1):
        result.append([path[i], path[i + 1]])
    
    return result
```

---

## 4. Cracking the Safe

### Objective

Find shortest string containing all n-digit passwords with k different digits.

### Examples

- LeetCode: Cracking the Safe.
- De Bruijn sequence generation.

### Approach

- Build graph where nodes are (n-1)-digit strings.
- Edges represent n-digit passwords.
- Find Eulerian path in this graph.

### Example: Cracking the Safe

```python
def crack_safe(n, k):
    if n == 1:
        return ''.join(str(i) for i in range(k))
    
    from collections import defaultdict
    
    graph = defaultdict(list)
    
    # Build graph
    start = '0' * (n - 1)
    for node_int in range(k ** (n - 1)):
        node = str(node_int).zfill(n - 1)
        for digit in range(k):
            graph[node].append(node[1:] + str(digit))
    
    # Hierholzer's algorithm
    stack = [start]
    path = []
    
    while stack:
        curr = stack[-1]
        
        if graph[curr]:
            stack.append(graph[curr].pop())
        else:
            path.append(stack.pop())
    
    path.reverse()
    
    # Build result
    result = path[0]
    for i in range(1, len(path)):
        result += path[i][-1]
    
    return result
```

---

## 5. Find Eulerian Path

### Objective

Find a path visiting all edges exactly once (not necessarily returning to start).

### Examples

- Drawing shapes without lifting pen.
- Graph traversal puzzles.

### Approach

- Check for existence (0 or 2 odd-degree vertices).
- Start from odd-degree vertex if exists.
- Apply Hierholzer's algorithm.

### Example: Eulerian Path

```python
from collections import defaultdict

def find_eulerian_path(n, edges):
    graph = defaultdict(list)
    degree = defaultdict(int)
    
    # Build graph
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
        degree[u] += 1
        degree[v] += 1
    
    # Check and find start vertex
    odd_vertices = [v for v in degree if degree[v] % 2 == 1]
    
    if len(odd_vertices) not in [0, 2]:
        return []  # No Eulerian path
    
    start = odd_vertices[0] if odd_vertices else edges[0][0]
    
    # Hierholzer's algorithm
    stack = [start]
    path = []
    
    while stack:
        curr = stack[-1]
        
        if graph[curr]:
            next_vertex = graph[curr].pop()
            graph[next_vertex].remove(curr)
            stack.append(next_vertex)
        else:
            path.append(stack.pop())
    
    return path[::-1]
```

---

## 6. Course Schedule IV (Directed Graph)

### Objective

Determine if all directed edges can be traversed in a single path.

### Examples

- Task dependency validation.
- Workflow path verification.

### Approach

- Check Eulerian path conditions for directed graph.
- Verify in-degree and out-degree conditions.
- Apply Hierholzer's if valid.

### Example: Directed Eulerian Path

```python
from collections import defaultdict

def find_directed_eulerian_path(edges):
    graph = defaultdict(list)
    in_degree = defaultdict(int)
    out_degree = defaultdict(int)
    vertices = set()
    
    # Build graph
    for u, v in edges:
        graph[u].append(v)
        out_degree[u] += 1
        in_degree[v] += 1
        vertices.add(u)
        vertices.add(v)
    
    # Check Eulerian path conditions
    start_nodes = 0
    end_nodes = 0
    start_vertex = edges[0][0]
    
    for v in vertices:
        diff = out_degree[v] - in_degree[v]
        
        if diff == 1:
            start_nodes += 1
            start_vertex = v
        elif diff == -1:
            end_nodes += 1
        elif diff != 0:
            return []  # No Eulerian path
    
    if not ((start_nodes == 0 and end_nodes == 0) or 
            (start_nodes == 1 and end_nodes == 1)):
        return []
    
    # Hierholzer's algorithm
    stack = [start_vertex]
    path = []
    
    while stack:
        curr = stack[-1]
        
        if graph[curr]:
            stack.append(graph[curr].pop())
        else:
            path.append(stack.pop())
    
    path.reverse()
    
    return path if len(path) == len(edges) + 1 else []
```

---

## 7. Minimum Arrows to Burst Balloons (Modified)

### Objective

Find if all intervals can be connected in a chain.

### Examples

- Interval chaining problems.
- Sequence connectivity validation.

### Approach

- Model intervals as directed edges.
- Check if Eulerian path exists.
- Find the valid ordering.

### Example: Chain Intervals

```python
from collections import defaultdict

def can_chain_intervals(intervals):
    graph = defaultdict(list)
    in_degree = defaultdict(int)
    out_degree = defaultdict(int)
    
    # Build graph from intervals
    for start, end in intervals:
        graph[start].append(end)
        out_degree[start] += 1
        in_degree[end] += 1
    
    # Check Eulerian path existence
    start_nodes = sum(1 for v in set(list(in_degree.keys()) + list(out_degree.keys()))
                      if out_degree[v] - in_degree[v] == 1)
    end_nodes = sum(1 for v in set(list(in_degree.keys()) + list(out_degree.keys()))
                    if in_degree[v] - out_degree[v] == 1)
    
    return (start_nodes == 0 and end_nodes == 0) or \
           (start_nodes == 1 and end_nodes == 1)
```

---

## Tips for Solving Hierholzer-Based Problems

- **Always check existence conditions first** before attempting to find path.
- **Undirected graphs**: Check vertex degrees (all even for circuit, 0 or 2 odd for path).
- **Directed graphs**: Check in-degree vs out-degree balance.
- **Starting vertex matters**: For paths, start from the vertex with extra out-degree.
- **Use stack for DFS**: Ensures correct backtracking order.
- **Remove edges as you traverse**: Prevents revisiting edges.
- **Reverse final path**: Hierholzer's builds path in reverse order.
- **Handle disconnected components**: Check if all edges are reachable from start.
- **Sort neighbors**: When lexicographical order matters (like itinerary problems).

---

## Common Variations

- **Chinese Postman Problem**: Find shortest path visiting all edges (may repeat some).
- **De Bruijn Sequences**: Special Eulerian paths in specific graph structures.
- **Mixed Graphs**: Graphs with both directed and undirected edges.
- **Counting Eulerian Paths**: Count number of valid Eulerian paths.

---

## Advantages of Hierholzer's Algorithm

- Linear time complexity O(E).
- Elegant stack-based implementation.
- Works for both directed and undirected graphs.
- Naturally handles backtracking.
- Memory efficient with edge removal approach.

---

## Key Pattern Recognition

Use Hierholzer's algorithm when you see:
- "Visit every edge exactly once"
- "Reconstruct path/itinerary from segments"
- "Valid arrangement/ordering of pairs"
- "Drawing without lifting pen"
- "Eulerian path/circuit"
- "Chain of intervals/pairs"
- "Route reconstruction problems"

---

## Common Mistakes to Avoid

- Forgetting to check existence conditions.
- Starting from wrong vertex (must start from odd-degree vertex for paths).
- Not reversing the final path.
- Not removing edges during traversal (causes infinite loops).
- Forgetting to handle disconnected graphs.
- Assuming path exists without verification.

---

By mastering Hierholzer's algorithm and understanding Eulerian path conditions, you will be able to solve a wide range of graph traversal and path reconstruction problems efficiently in interviews.