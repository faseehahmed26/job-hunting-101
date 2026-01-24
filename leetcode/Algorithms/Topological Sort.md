# Topological Sort: An Overview

Topological Sort is a linear ordering of vertices in a Directed Acyclic Graph (DAG) where for every directed edge (u → v), vertex u comes before vertex v in the ordering. It's used to find a valid sequence when there are dependency constraints.

---

## Key Concepts

### Requirements

- Graph must be **directed** and **acyclic** (DAG)
- If there's a cycle, topological sort is impossible
- Multiple valid orderings may exist

### Two Main Approaches

1. **Kahn's Algorithm (BFS-based)**: Uses in-degree tracking
2. **DFS-based**: Uses recursion and stack

### Time Complexity

- Both approaches: O(V + E) where V = vertices, E = edges

---

## Kahn's Algorithm (BFS Approach)

### Algorithm Steps

1. Calculate in-degree (number of incoming edges) for each vertex
2. Add all vertices with in-degree 0 to queue
3. Process queue:
   - Remove vertex, add to result
   - Decrease in-degree of neighbors
   - Add neighbors with in-degree 0 to queue
4. If result has all vertices, return it; otherwise, cycle detected

### Implementation

```python
from collections import deque, defaultdict

def topological_sort_bfs(n, edges):
    # Build graph and calculate in-degrees
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    # Start with nodes having no dependencies
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # Reduce in-degree for neighbors
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Check if all nodes processed (no cycle)
    return result if len(result) == n else []
```

---

## DFS Approach

### Algorithm Steps

1. Mark all vertices as unvisited
2. For each unvisited vertex, perform DFS
3. After exploring all neighbors, add vertex to stack
4. Reverse the stack to get topological order

### Implementation

```python
def topological_sort_dfs(n, edges):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    visited = [False] * n
    stack = []
    
    def dfs(node):
        visited[node] = True
        
        for neighbor in graph[node]:
            if not visited[neighbor]:
                dfs(neighbor)
        
        stack.append(node)  # Add after exploring all children
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    return stack[::-1]  # Reverse for topological order
```

---

## When to Use Topological Sort

Use topological sort when you need to:
- Order tasks with dependencies
- Find valid execution sequence
- Detect cycles in directed graphs
- Schedule jobs with prerequisites
- Resolve compilation/build order

**Key indicators**:
- "Course prerequisites"
- "Task dependencies"
- "Build/compilation order"
- "Valid sequence/ordering"
- "Finish X before Y"

---

## Common Problem Patterns

## 1. Course Schedule (Detect Cycle)

Check if all courses can be completed given prerequisites.

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
    
    return completed == num_courses
```

---

## 2. Course Schedule II (Return Order)

Return a valid course order or empty if impossible.

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

## 3. Alien Dictionary

Determine character order from sorted alien words.

```python
def alien_order(words):
    # Build graph from word comparisons
    graph = defaultdict(set)
    in_degree = {c: 0 for word in words for c in word}
    
    for i in range(len(words) - 1):
        word1, word2 = words[i], words[i + 1]
        min_len = min(len(word1), len(word2))
        
        # Invalid case: word1 is prefix of word2 but comes after
        if len(word1) > len(word2) and word1[:min_len] == word2[:min_len]:
            return ""
        
        # Find first different character
        for j in range(min_len):
            if word1[j] != word2[j]:
                if word2[j] not in graph[word1[j]]:
                    graph[word1[j]].add(word2[j])
                    in_degree[word2[j]] += 1
                break
    
    # Topological sort
    queue = deque([c for c in in_degree if in_degree[c] == 0])
    result = []
    
    while queue:
        char = queue.popleft()
        result.append(char)
        
        for neighbor in graph[char]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return "".join(result) if len(result) == len(in_degree) else ""
```

---

## 4. Parallel Courses (Minimum Time)

Find minimum semesters needed with course dependencies.

```python
def minimum_semesters(n, relations):
    graph = defaultdict(list)
    in_degree = [0] * (n + 1)
    
    for prev, next in relations:
        graph[prev].append(next)
        in_degree[next] += 1
    
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

## 5. Build Order / Compilation Order

Determine valid build sequence for projects.

```python
def build_order(projects, dependencies):
    graph = defaultdict(list)
    in_degree = {p: 0 for p in projects}
    
    for before, after in dependencies:
        graph[before].append(after)
        in_degree[after] += 1
    
    queue = deque([p for p in projects if in_degree[p] == 0])
    order = []
    
    while queue:
        project = queue.popleft()
        order.append(project)
        
        for dependent in graph[project]:
            in_degree[dependent] -= 1
            if in_degree[dependent] == 0:
                queue.append(dependent)
    
    return order if len(order) == len(projects) else None
```

---

## 6. Sequence Reconstruction

Check if sequence is the only valid topological sort.

```python
def sequence_reconstruction(org, seqs):
    # Build graph from sequences
    graph = defaultdict(set)
    in_degree = defaultdict(int)
    nodes = set()
    
    for seq in seqs:
        for num in seq:
            nodes.add(num)
        for i in range(len(seq) - 1):
            if seq[i + 1] not in graph[seq[i]]:
                graph[seq[i]].add(seq[i + 1])
                in_degree[seq[i + 1]] += 1
    
    if nodes != set(org):
        return False
    
    queue = deque([n for n in nodes if in_degree[n] == 0])
    idx = 0
    
    while queue:
        if len(queue) > 1:  # Multiple valid orders exist
            return False
        
        node = queue.popleft()
        if idx >= len(org) or node != org[idx]:
            return False
        
        idx += 1
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return idx == len(org)
```

---

## Cycle Detection Using DFS

Track visiting states to detect cycles during topological sort.

```python
def has_cycle_dfs(n, edges):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    # 0: unvisited, 1: visiting, 2: visited
    state = [0] * n
    
    def dfs(node):
        if state[node] == 1:  # Currently visiting (cycle!)
            return True
        if state[node] == 2:  # Already visited
            return False
        
        state[node] = 1  # Mark as visiting
        
        for neighbor in graph[node]:
            if dfs(neighbor):
                return True
        
        state[node] = 2  # Mark as visited
        return False
    
    for i in range(n):
        if state[i] == 0:
            if dfs(i):
                return True
    
    return False
```

---

## Tips

- **Kahn's (BFS)** is better when you need to detect cycles easily or track levels/layers
- **DFS** is simpler to code but requires cycle detection logic for validation
- Always check if result length equals number of nodes (ensures no cycle)
- For "minimum time/semesters" problems, track depth/level in BFS
- Use state array (unvisited/visiting/visited) in DFS to detect cycles
- If multiple orderings exist, problem may ask for lexicographically smallest (use min-heap)

---

## Advantages

- Efficiently orders elements with dependencies in O(V + E)
- Natural fit for scheduling and prerequisite problems
- Can detect cycles as a byproduct
- Works with both sparse and dense graphs

---

By mastering topological sort, you'll solve ordering, scheduling, and dependency problems efficiently in interviews.