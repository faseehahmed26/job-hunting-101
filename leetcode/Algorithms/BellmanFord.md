# Bellman-Ford Algorithm: An Overview

Bellman-Ford is a shortest path algorithm that can handle graphs with **negative edge weights**. Unlike Dijkstra's, it can detect negative cycles and works on graphs where edge weights can be negative.

---

## Key Concepts

### Core Idea

- Relax all edges repeatedly (V-1) times
- **Relaxation**: Update distance if a shorter path is found
- After V-1 iterations, shortest paths are found (if no negative cycle)
- One more iteration detects negative cycles

### Why V-1 Iterations?

- Shortest path between any two vertices has at most V-1 edges
- Each iteration finds paths that are one edge longer
- After V-1 iterations, all shortest paths are discovered

### Time & Space Complexity

- **Time**: O(V × E) - relax all edges V-1 times
- **Space**: O(V) - distance array

---

## Basic Implementation

```python
def bellman_ford(n, edges, source):
    # Initialize distances
    distance = [float('inf')] * n
    distance[source] = 0
    
    # Relax all edges V-1 times
    for _ in range(n - 1):
        for u, v, weight in edges:
            if distance[u] != float('inf') and distance[u] + weight < distance[v]:
                distance[v] = distance[u] + weight
    
    # Check for negative cycles
    for u, v, weight in edges:
        if distance[u] != float('inf') and distance[u] + weight < distance[v]:
            return None  # Negative cycle detected
    
    return distance
```

---

## When to Use Bellman-Ford

Use Bellman-Ford when you need to:
- Handle graphs with negative edge weights
- Detect negative cycles
- Find shortest paths from single source
- Work with edge list representation

**Key indicators**:
- "Negative weights allowed"
- "Detect negative cycles"
- "Arbitrage opportunities" (currency exchange)
- "Shortest path with constraints"

**Use Dijkstra instead if**:
- All edge weights are non-negative
- Need faster performance

---

## Common Problem Patterns

## 1. Network Delay Time (Basic Shortest Path)

Find time for signal to reach all nodes.

```python
def network_delay_time(times, n, k):
    distance = [float('inf')] * (n + 1)
    distance[k] = 0
    
    # Relax edges n-1 times
    for _ in range(n - 1):
        for u, v, w in times:
            if distance[u] != float('inf'):
                distance[v] = min(distance[v], distance[u] + w)
    
    max_dist = max(distance[1:])
    return max_dist if max_dist != float('inf') else -1
```

---

## 2. Cheapest Flights Within K Stops

Find cheapest path with at most K stops.

```python
def find_cheapest_price(n, flights, src, dst, k):
    # Use Bellman-Ford with limited iterations (k+1)
    distance = [float('inf')] * n
    distance[src] = 0
    
    # Run k+1 iterations (k stops means k+1 edges)
    for _ in range(k + 1):
        temp = distance.copy()  # Prevent same iteration updates
        
        for u, v, price in flights:
            if distance[u] != float('inf'):
                temp[v] = min(temp[v], distance[u] + price)
        
        distance = temp
    
    return distance[dst] if distance[dst] != float('inf') else -1
```

---

## 3. Detect Negative Cycle

Check if graph contains a negative weight cycle.

```python
def has_negative_cycle(n, edges):
    distance = [0] * n  # Start with 0 to detect any cycle
    
    # Relax edges n-1 times
    for _ in range(n - 1):
        for u, v, weight in edges:
            if distance[u] + weight < distance[v]:
                distance[v] = distance[u] + weight
    
    # Check for negative cycle
    for u, v, weight in edges:
        if distance[u] + weight < distance[v]:
            return True  # Negative cycle exists
    
    return False
```

---

## 4. Currency Arbitrage Detection

Find if arbitrage opportunity exists (profit from currency exchange).

```python
import math

def has_arbitrage(n, rates):
    # Convert to edge list with negative log weights
    # If product > 1, sum of logs > 0, negative sum < 0
    edges = []
    for i in range(n):
        for j in range(n):
            if i != j:
                # Negative log to convert product to sum
                edges.append((i, j, -math.log(rates[i][j])))
    
    # Detect negative cycle (arbitrage opportunity)
    distance = [0] * n
    
    for _ in range(n - 1):
        for u, v, weight in edges:
            if distance[u] + weight < distance[v]:
                distance[v] = distance[u] + weight
    
    for u, v, weight in edges:
        if distance[u] + weight < distance[v]:
            return True  # Arbitrage exists
    
    return False
```

---

## 5. Path with Limited Edges

Find shortest path using at most K edges.

```python
def shortest_path_k_edges(n, edges, source, k):
    distance = [float('inf')] * n
    distance[source] = 0
    
    # Run exactly k iterations
    for _ in range(k):
        temp = distance.copy()
        
        for u, v, weight in edges:
            if distance[u] != float('inf'):
                temp[v] = min(temp[v], distance[u] + weight)
        
        distance = temp
    
    return distance
```

---

## 6. Minimum Cost with Discounts

Find shortest path where you can use discount coupons.

```python
def minimum_cost_with_discounts(n, edges, source, target, discounts):
    # State: (node, discounts_used)
    # Use modified Bellman-Ford
    distance = [[float('inf')] * (discounts + 1) for _ in range(n)]
    distance[source][0] = 0
    
    for _ in range(n - 1):
        updated = False
        for u, v, cost in edges:
            for d in range(discounts + 1):
                if distance[u][d] != float('inf'):
                    # Don't use discount
                    if distance[u][d] + cost < distance[v][d]:
                        distance[v][d] = distance[u][d] + cost
                        updated = True
                    
                    # Use discount (half price)
                    if d < discounts and distance[u][d] + cost // 2 < distance[v][d + 1]:
                        distance[v][d + 1] = distance[u][d] + cost // 2
                        updated = True
        
        if not updated:
            break
    
    return min(distance[target])
```

---

## 7. Shortest Path with Node Weights

Find shortest path considering both edge and node weights.

```python
def shortest_path_node_weights(n, edges, node_weights, source):
    distance = [float('inf')] * n
    distance[source] = node_weights[source]
    
    for _ in range(n - 1):
        for u, v, edge_weight in edges:
            if distance[u] != float('inf'):
                # Add destination node weight
                new_dist = distance[u] + edge_weight + node_weights[v]
                distance[v] = min(distance[v], new_dist)
    
    return distance
```

---

## Bellman-Ford with Path Reconstruction

Track parent pointers to reconstruct the shortest path.

```python
def bellman_ford_with_path(n, edges, source, target):
    distance = [float('inf')] * n
    parent = [-1] * n
    distance[source] = 0
    
    # Relax edges
    for _ in range(n - 1):
        for u, v, weight in edges:
            if distance[u] != float('inf') and distance[u] + weight < distance[v]:
                distance[v] = distance[u] + weight
                parent[v] = u
    
    # Check negative cycle
    for u, v, weight in edges:
        if distance[u] != float('inf') and distance[u] + weight < distance[v]:
            return None, []  # Negative cycle
    
    # Reconstruct path
    if distance[target] == float('inf'):
        return float('inf'), []
    
    path = []
    node = target
    while node != -1:
        path.append(node)
        node = parent[node]
    
    return distance[target], path[::-1]
```

---

## Optimization: Early Termination

Stop early if no updates occur in an iteration.

```python
def bellman_ford_optimized(n, edges, source):
    distance = [float('inf')] * n
    distance[source] = 0
    
    for i in range(n - 1):
        updated = False
        
        for u, v, weight in edges:
            if distance[u] != float('inf') and distance[u] + weight < distance[v]:
                distance[v] = distance[u] + weight
                updated = True
        
        if not updated:
            break  # No changes, can stop early
    
    # Check negative cycle
    for u, v, weight in edges:
        if distance[u] != float('inf') and distance[u] + weight < distance[v]:
            return None
    
    return distance
```

---

## Tips

- **Copy distance array** when limiting iterations (K stops problem) to prevent same-iteration updates
- **Initialize source to 0**, others to infinity
- Check `distance[u] != float('inf')` before relaxing to avoid propagating infinity
- **Negative cycle detection**: One more iteration after V-1; if any update occurs, cycle exists
- For **K edges/stops**: Run exactly K iterations instead of V-1
- **Early termination**: If no updates in iteration, shortest paths found
- Use **negative log** for currency exchange to convert multiplication to addition

---

## Bellman-Ford vs Dijkstra

| Aspect | Bellman-Ford | Dijkstra |
|--------|-------------|----------|
| Negative weights | Yes | No |
| Negative cycle detection | Yes | No |
| Time complexity | O(V × E) | O((V + E) log V) |
| Best for | Sparse graphs, negative weights | Dense graphs, positive weights |
| Implementation | Simple edge relaxation | Priority queue required |

---

## Advantages

- Handles negative edge weights
- Detects negative cycles
- Simple implementation
- Works with edge list (no adjacency list needed)
- Flexible for constraints (K edges, discounts, etc.)

---

## Limitations

- Slower than Dijkstra for positive weights
- Doesn't work with negative cycles (undefined shortest path)
- O(V × E) can be slow for dense graphs

---

By mastering Bellman-Ford, you'll solve shortest path problems with negative weights and detect negative cycles efficiently in interviews.