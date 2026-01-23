# Dijkstra's Algorithm: An Overview

Dijkstra's algorithm is a graph traversal algorithm that finds the shortest path from a source node to all other nodes in a weighted graph with non-negative edge weights. It uses a greedy approach combined with a priority queue to efficiently compute minimum distances.

---

## Key Concepts of Dijkstra's Algorithm

### Components

- **Distance array**: Tracks the shortest known distance from source to each node.
- **Priority queue (min-heap)**: Stores nodes to visit, ordered by current shortest distance.
- **Visited set**: Prevents reprocessing nodes.

### Algorithm Flow

1. Initialize all distances to infinity except the source (distance 0).
2. Add source to priority queue.
3. Extract node with minimum distance.
4. For each neighbor, relax the edge (update distance if shorter path found).
5. Repeat until priority queue is empty.

### Time Complexity

- **With binary heap**: O((V + E) log V)
- **With Fibonacci heap**: O(E + V log V)
- V = vertices, E = edges

---

## Types of Problems Solved Using Dijkstra's Algorithm

## 1. Single Source Shortest Path

### Objective

Find the shortest path from a source node to all other nodes.

### Examples

- Network routing protocols.
- GPS navigation systems.

### Approach

- Run standard Dijkstra from source node.
- Distance array contains shortest paths to all nodes.

### Example: Basic Dijkstra

```python
import heapq

def dijkstra(graph, start):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]  # (distance, node)
    
    while pq:
        curr_dist, curr_node = heapq.heappop(pq)
        
        if curr_dist > distances[curr_node]:
            continue
        
        for neighbor, weight in graph[curr_node]:
            distance = curr_dist + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances
```

---

## 2. Shortest Path Between Two Nodes

### Objective

Find the shortest path from source to a specific target node.

### Examples

- Finding shortest route on a map.
- Network packet routing.

### Approach

- Run Dijkstra but terminate early when target is reached.
- Reconstruct path using parent pointers.

### Example: Path Reconstruction

```python
import heapq

def dijkstra_path(graph, start, end):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    parent = {start: None}
    pq = [(0, start)]
    
    while pq:
        curr_dist, curr_node = heapq.heappop(pq)
        
        if curr_node == end:
            break
        
        if curr_dist > distances[curr_node]:
            continue
        
        for neighbor, weight in graph[curr_node]:
            distance = curr_dist + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                parent[neighbor] = curr_node
                heapq.heappush(pq, (distance, neighbor))
    
    # Reconstruct path
    path = []
    node = end
    while node is not None:
        path.append(node)
        node = parent.get(node)
    
    return path[::-1], distances[end]
```

---

## 3. Grid-Based Shortest Path

### Objective

Find shortest path in a 2D grid with obstacles or varying costs.

### Examples

- Robot path planning.
- Game AI pathfinding.

### Approach

- Convert grid to graph representation.
- Each cell is a node, edges to adjacent cells.
- Use Dijkstra with coordinate pairs.

### Example: Grid Dijkstra

```python
import heapq

def dijkstra_grid(grid):
    rows, cols = len(grid), len(grid[0])
    distances = [[float('inf')] * cols for _ in range(rows)]
    distances[0][0] = grid[0][0]
    pq = [(grid[0][0], 0, 0)]  # (distance, row, col)
    directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
    
    while pq:
        curr_dist, r, c = heapq.heappop(pq)
        
        if curr_dist > distances[r][c]:
            continue
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < rows and 0 <= nc < cols:
                distance = curr_dist + grid[nr][nc]
                
                if distance < distances[nr][nc]:
                    distances[nr][nc] = distance
                    heapq.heappush(pq, (distance, nr, nc))
    
    return distances[rows-1][cols-1]
```

---

## 4. Modified Weight Problems

### Objective

Solve shortest path with transformations or constraints on weights.

### Examples

- Path with minimum cost where you can halve one edge weight.
- Network delay time (weighted directed graph).

### Approach

- Modify the state in priority queue to include additional information.
- Track multiple states per node if needed.

### Example: Network Delay Time

```python
import heapq
from collections import defaultdict

def network_delay_time(times, n, k):
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))
    
    distances = {i: float('inf') for i in range(1, n + 1)}
    distances[k] = 0
    pq = [(0, k)]
    
    while pq:
        curr_time, node = heapq.heappop(pq)
        
        if curr_time > distances[node]:
            continue
        
        for neighbor, time in graph[node]:
            new_time = curr_time + time
            
            if new_time < distances[neighbor]:
                distances[neighbor] = new_time
                heapq.heappush(pq, (new_time, neighbor))
    
    max_time = max(distances.values())
    return max_time if max_time != float('inf') else -1
```

---

## 5. K-Stops Shortest Path

### Objective

Find shortest path with at most K intermediate stops.

### Examples

- Cheapest flights with K stops.
- Limited hop routing.

### Approach

- Track both distance and number of stops in state.
- Use modified Dijkstra with (cost, node, stops) tuples.

### Example: Cheapest Flights Within K Stops

```python
import heapq
from collections import defaultdict

def find_cheapest_price(n, flights, src, dst, k):
    graph = defaultdict(list)
    for u, v, price in flights:
        graph[u].append((v, price))
    
    pq = [(0, src, 0)]  # (cost, node, stops)
    visited = {}
    
    while pq:
        cost, node, stops = heapq.heappop(pq)
        
        if node == dst:
            return cost
        
        if stops > k:
            continue
        
        if node in visited and visited[node] <= stops:
            continue
        
        visited[node] = stops
        
        for neighbor, price in graph[node]:
            heapq.heappush(pq, (cost + price, neighbor, stops + 1))
    
    return -1
```

---

## 6. Multi-Source Shortest Path

### Objective

Find shortest distances from multiple source nodes simultaneously.

### Examples

- Nearest hospital/police station from any location.
- Multi-source BFS variations.

### Approach

- Initialize all source nodes with distance 0.
- Add all sources to priority queue initially.

### Example: Multi-Source Dijkstra

```python
import heapq

def multi_source_dijkstra(graph, sources):
    distances = {node: float('inf') for node in graph}
    pq = []
    
    for source in sources:
        distances[source] = 0
        heapq.heappush(pq, (0, source))
    
    while pq:
        curr_dist, curr_node = heapq.heappop(pq)
        
        if curr_dist > distances[curr_node]:
            continue
        
        for neighbor, weight in graph[curr_node]:
            distance = curr_dist + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances
```

---

## 7. Path with Minimum Effort

### Objective

Minimize the maximum absolute difference along a path.

### Examples

- Hiking trail with minimum elevation change.
- Path with minimum maximum edge weight.

### Approach

- Use Dijkstra where distance represents maximum effort so far.
- Update using max(current_effort, edge_weight).

### Example: Path With Minimum Effort

```python
import heapq

def minimum_effort_path(heights):
    rows, cols = len(heights), len(heights[0])
    efforts = [[float('inf')] * cols for _ in range(rows)]
    efforts[0][0] = 0
    pq = [(0, 0, 0)]  # (effort, row, col)
    directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
    
    while pq:
        effort, r, c = heapq.heappop(pq)
        
        if r == rows - 1 and c == cols - 1:
            return effort
        
        if effort > efforts[r][c]:
            continue
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < rows and 0 <= nc < cols:
                new_effort = max(effort, abs(heights[nr][nc] - heights[r][c]))
                
                if new_effort < efforts[nr][nc]:
                    efforts[nr][nc] = new_effort
                    heapq.heappush(pq, (new_effort, nr, nc))
    
    return 0
```

---

## Tips for Solving Dijkstra Problems

- Ensure all edge weights are non-negative (use Bellman-Ford for negative weights).
- Use a min-heap (priority queue) for efficiency.
- Track visited nodes or check if current distance is outdated to avoid redundant work.
- For path reconstruction, maintain a parent/predecessor map.
- Consider state representation carefully for modified problems (include extra dimensions if needed).
- Always initialize source distance to 0 and others to infinity.
- Handle unreachable nodes by checking for infinity in final distances.

---

## Common Variations

- **A* Algorithm**: Dijkstra with heuristic for faster target finding.
- **Bidirectional Dijkstra**: Run from both source and target simultaneously.
- **Dial's Algorithm**: Optimization for integer weights.
- **All-Pairs Shortest Path**: Use Floyd-Warshall instead.

---

## Advantages of Dijkstra's Algorithm

- Guarantees optimal solution for non-negative weights.
- More efficient than Bellman-Ford for this case.
- Works on both directed and undirected graphs.
- Can be adapted to various problem constraints.

---

## Limitations

- Cannot handle negative edge weights.
- Less efficient than BFS for unweighted graphs.
- May visit many nodes in dense graphs.

---

By mastering Dijkstra's algorithm and recognizing its patterns, you will be able to solve a wide range of shortest path and graph optimization problems efficiently in interviews.