# Kahn's Algorithm (BFS): An Overview

Kahn's algorithm is a BFS-based approach for topological sorting of a Directed Acyclic Graph (DAG). It processes nodes in order of their dependencies by tracking in-degrees (number of incoming edges) and removing nodes with no dependencies first.

---

## Key Concepts

### Core Idea

- **In-degree**: Number of incoming edges to a node (prerequisites/dependencies)
- Process nodes with in-degree 0 first (no dependencies)
- After processing a node, reduce in-degree of its neighbors
- Continue until all nodes processed or cycle detected

### Algorithm Steps

1. Calculate in-degree for all nodes
2. Add all nodes with in-degree 0 to queue
3. While queue not empty:
   - Dequeue a node and add to result
   - For each neighbor, decrease in-degree by 1
   - If neighbor's in-degree becomes 0, add to queue
4. If result contains all nodes → valid topological order
   If not → cycle exists

### Time & Space Complexity

- **Time**: O(V + E) - visit each vertex and edge once
- **Space**: O(V) - for queue and in-degree array

---

## Basic Implementation

```python
from collections import deque, defaultdict

def kahns_algorithm(n, edges):
    # Build adjacency list and calculate in-degrees
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:  # Edge from u to v
        graph[u].append(v)
        in_degree[v] += 1
    
    # Initialize queue with nodes having no dependencies
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # Process neighbors
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            
            # If all dependencies satisfied, add to queue
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Check if topological sort is possible (no cycle)
    if len(result) == n:
        return result
    else:
        return []  # Cycle detected
```

---

## When to Use Kahn's Algorithm

Use Kahn's BFS when you need to:
- Find topological ordering of tasks/courses
- Detect cycles in directed graphs
- Process items level by level (track stages/rounds)
- Find minimum number of steps/rounds needed

**Key indicators**:
- "Prerequisites" or "dependencies"
- "Valid order/sequence"
- "Minimum semesters/rounds"
- "Detect circular dependencies"

---

## Common Problem Patterns

## 1. Course Schedule (Cycle Detection)

Determine if all courses can be finished.

```python
def can_finish(num_courses, prerequisites):
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    completed = 0
    
    while queue:
        course = queue.popleft()
        completed += 1
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return completed == num_courses  # True if no cycle
```

---

## 2. Course Schedule II (Return Order)

Return valid course order or empty if impossible.

```python
def find_order(num_courses, prerequisites):
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    order = []
    
    while queue:
        course = queue.popleft()
        order.append(course)
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return order if len(order) == num_courses else []
```

---

## 3. Minimum Semesters/Rounds

Find minimum number of rounds to complete all tasks.

```python
def minimum_semesters(n, relations):
    graph = defaultdict(list)
    in_degree = [0] * (n + 1)
    
    for prev, next in relations:
        graph[prev].append(next)
        in_degree[next] += 1
    
    # Queue stores (course, semester_number)
    queue = deque([(i, 1) for i in range(1, n + 1) if in_degree[i] == 0])
    completed = 0
    max_semester = 0
    
    while queue:
        course, semester = queue.popleft()
        completed += 1
        max_semester = max(max_semester, semester)
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append((next_course, semester + 1))
    
    return max_semester if completed == n else -1
```

---

## 4. All Ancestors in DAG

Find all ancestors for each node.

```python
def get_ancestors(n, edges):
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    # Each node tracks its ancestors
    ancestors = [set() for _ in range(n)]
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    
    while queue:
        node = queue.popleft()
        
        for neighbor in graph[node]:
            # Neighbor inherits all ancestors from node
            ancestors[neighbor].add(node)
            ancestors[neighbor].update(ancestors[node])
            
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return [sorted(list(anc)) for anc in ancestors]
```

---

## 5. Parallel Courses III (Maximum Time)

Find minimum time when courses can be taken in parallel.

```python
def minimum_time(n, relations, time):
    graph = defaultdict(list)
    in_degree = [0] * (n + 1)
    
    for prev, next in relations:
        graph[prev].append(next)
        in_degree[next] += 1
    
    # Track earliest completion time for each course
    completion_time = [0] * (n + 1)
    queue = deque()
    
    for i in range(1, n + 1):
        if in_degree[i] == 0:
            completion_time[i] = time[i - 1]
            queue.append(i)
    
    while queue:
        course = queue.popleft()
        
        for next_course in graph[course]:
            # Next course can start after current finishes
            completion_time[next_course] = max(
                completion_time[next_course],
                completion_time[course] + time[next_course - 1]
            )
            
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return max(completion_time)
```

---

## 6. Lexicographically Smallest Ordering

Find smallest valid topological order.

```python
import heapq

def smallest_topological_order(n, edges):
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    # Use min-heap instead of queue for lexicographic order
    heap = [i for i in range(n) if in_degree[i] == 0]
    heapq.heapify(heap)
    result = []
    
    while heap:
        node = heapq.heappop(heap)
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                heapq.heappush(heap, neighbor)
    
    return result if len(result) == n else []
```

---

## 7. Build Order with Groups

Process tasks in groups/batches level by level.

```python
def build_in_batches(n, edges):
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    batches = []
    
    while queue:
        # Process entire level at once
        batch_size = len(queue)
        current_batch = []
        
        for _ in range(batch_size):
            node = queue.popleft()
            current_batch.append(node)
            
            for neighbor in graph[node]:
                in_degree[neighbor] -= 1
                if in_degree[neighbor] == 0:
                    queue.append(neighbor)
        
        batches.append(current_batch)
    
    # Verify all nodes processed
    total_nodes = sum(len(batch) for batch in batches)
    return batches if total_nodes == n else []
```

---

## Tips

- **In-degree tracking** is the key: nodes with 0 in-degree have no dependencies
- Use **regular queue** for any valid order, **min-heap** for lexicographically smallest
- Track **levels/rounds** by processing queue in batches or storing level with each node
- If `len(result) != n`, there's a **cycle** in the graph
- For **time-based problems**, track earliest completion time for each node
- **Space optimization**: Can use array instead of defaultdict if nodes are 0 to n-1
- BFS nature makes it easy to track levels/stages (unlike DFS approach)

---

## Advantages of Kahn's Algorithm

- Easy to detect cycles (if result.length < n)
- Natural for level-by-level processing
- Intuitive understanding (remove dependencies one by one)
- Easy to track additional information (time, level, ancestors)
- More intuitive than DFS for most people

---

## Common Variations

- **Minimum height trees**: Find nodes that minimize tree height when used as root
- **Build order with priorities**: Use priority queue for weighted dependencies
- **Multi-source BFS**: Start with multiple nodes having in-degree 0
- **Bidirectional dependencies**: Check if valid ordering exists

---

By mastering Kahn's algorithm, you'll efficiently solve dependency ordering, scheduling, and prerequisite problems in interviews.