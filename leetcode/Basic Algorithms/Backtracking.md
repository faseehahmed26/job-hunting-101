````md
https://leetcode.com/tag/backtracking/

https://leetcode.com/explore/learn/card/recursion-ii/472/backtracking/

https://www.geeksforgeeks.org/backtracking-algorithms/

# Backtracking: Complete Interview Guide

Backtracking is an algorithmic paradigm used to explore **all possible solutions** by building solutions incrementally and abandoning paths that violate constraints.  
It is essentially **DFS on a decision tree with undo operations**.

---

## Core Concepts

### What is Backtracking?
- Explore choices step by step
- Build partial solutions
- Revert choices when constraints fail
- Guarantees correctness by exploring all valid paths

### When to Use Backtracking?
- Generate **all possible solutions**
- Search problems with constraints
- Problems involving **combinations, permutations, subsets**
- Grid or board-based problems (Sudoku, N-Queens)
- Decision problems with branching choices

---

## Key Characteristics

### Recursive Exploration
- Each recursive call represents a decision
- Depth represents number of decisions made

### Decision Tree
- Nodes = states
- Edges = choices
- Leaves = valid solutions or dead ends

### Pruning
- Skip paths early if constraints fail
- Improves performance significantly

---

## Universal Backtracking Template

```python
def backtrack(state):
    # Base case
    if is_solution(state):
        record_solution(state)
        return

    for choice in choices(state):
        if not is_valid(choice, state):
            continue

        apply(choice, state)
        backtrack(state)
        undo(choice, state)
````

### Template Rules

* Always undo changes after recursion
* Validate constraints early (pruning)
* Use recursion depth as progress indicator

---

## Problem Categories and Patterns

## 1. Permutations

### Objective

Generate all permutations of elements.

```python
def permute(nums):
    result = []

    def backtrack(path, used):
        if len(path) == len(nums):
            result.append(path[:])
            return

        for i in range(len(nums)):
            if used[i]:
                continue
            used[i] = True
            path.append(nums[i])
            backtrack(path, used)
            path.pop()
            used[i] = False

    backtrack([], [False] * len(nums))
    return result
```

**Time Complexity:** O(n!)
**Space Complexity:** O(n)

---

## 2. Combinations

### Objective

Choose k elements from n.

```python
def combine(n, k):
    result = []

    def backtrack(start, path):
        if len(path) == k:
            result.append(path[:])
            return

        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()

    backtrack(1, [])
    return result
```

**Time Complexity:** O(C(n, k))
**Space Complexity:** O(k)

---

## 3. Subsets (Power Set)

### Objective

Generate all subsets.

```python
def subsets(nums):
    result = []

    def backtrack(index, path):
        result.append(path[:])
        for i in range(index, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()

    backtrack(0, [])
    return result
```

**Time Complexity:** O(2ⁿ × n)
**Space Complexity:** O(n)

---

## 4. N-Queens

### Objective

Place n queens so none attack each other.

```python
def solveNQueens(n):
    result = []
    board = [["."] * n for _ in range(n)]
    cols, diag1, diag2 = set(), set(), set()

    def backtrack(r):
        if r == n:
            result.append(["".join(row) for row in board])
            return

        for c in range(n):
            if c in cols or r - c in diag1 or r + c in diag2:
                continue

            board[r][c] = "Q"
            cols.add(c)
            diag1.add(r - c)
            diag2.add(r + c)

            backtrack(r + 1)

            board[r][c] = "."
            cols.remove(c)
            diag1.remove(r - c)
            diag2.remove(r + c)

    backtrack(0)
    return result
```

**Time Complexity:** O(n!)
**Space Complexity:** O(n)

---

## 5. Sudoku Solver

### Objective

Fill a partially completed Sudoku grid.

```python
def solveSudoku(board):
    def is_valid(r, c, num):
        for i in range(9):
            if board[r][i] == num or board[i][c] == num:
                return False
            if board[3*(r//3)+i//3][3*(c//3)+i%3] == num:
                return False
        return True

    def backtrack():
        for r in range(9):
            for c in range(9):
                if board[r][c] == ".":
                    for num in map(str, range(1, 10)):
                        if is_valid(r, c, num):
                            board[r][c] = num
                            if backtrack():
                                return True
                            board[r][c] = "."
                    return False
        return True

    backtrack()
```

**Worst Time Complexity:** O(9^(n²))
**Space Complexity:** O(n²)

---

## 6. Word Search (Grid Backtracking)

### Objective

Check if a word exists in a grid.

```python
def word_search(board, word):
    rows, cols = len(board), len(board[0])

    def backtrack(r, c, idx):
        if idx == len(word):
            return True
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return False
        if board[r][c] != word[idx]:
            return False

        temp = board[r][c]
        board[r][c] = "#"

        found = (
            backtrack(r+1, c, idx+1) or
            backtrack(r-1, c, idx+1) or
            backtrack(r, c+1, idx+1) or
            backtrack(r, c-1, idx+1)
        )

        board[r][c] = temp
        return found

    for i in range(rows):
        for j in range(cols):
            if backtrack(i, j, 0):
                return True
    return False
```

**Time Complexity:** O(m × n × 4ᴸ)
**Space Complexity:** O(L)

---

## Common Techniques and Tricks

### Pruning

* Check constraints before recursion
* Skip invalid branches early

### Constraints First

* Validate small constraints early to reduce recursion

### Hash Sets

* Use sets for O(1) constraint checks

### Undo Operations

* Always revert state changes after recursion

---

## Complexity Summary

| Problem Type | Time  | Space |
| ------------ | ----- | ----- |
| Permutations | O(n!) | O(n)  |
| Subsets      | O(2ⁿ) | O(n)  |
| N-Queens     | O(n!) | O(n)  |
| Word Search  | O(4ᴸ) | O(L)  |

---

## Interview Recognition Signals

### Keywords

* Generate all
* Return all possible
* Any valid configuration
* Path, choice, decision
* Grid, board, placements

### Characteristics

* Exponential search space
* Recursive structure
* Constraints involved

---

## Common Mistakes

* Forgetting to undo changes
* Missing base cases
* Not pruning early
* Using global state incorrectly

---

## Practice Problems

### Easy

* Subsets (78)
* Permutations (46)

### Medium

* Combination Sum (39)
* Word Search (79)

### Hard

* N-Queens (51)
* Sudoku Solver (37)

---

**Key Takeaway**
Backtracking is DFS with undo. If the problem asks for *all possible solutions* under constraints, backtracking is your first instinct.

```
```
