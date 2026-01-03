# Time Complexity Formula Sheet (Memorize-Ready)

## Core counting formulas
- Subsets / subsequences: **2^n**
- Permutations of n distinct: **n!**
- Permutations of length k: **nPk = n! / (n-k)!**
- Combinations: **nCk = n! / (k!(n-k)!)**

## Common recursion patterns
- T(n) = T(n-1) + O(1) → **O(n)**
- T(n) = T(n-1) + T(n-2) → **O(2^n)** (rough)
- T(n) = 2T(n-1) + O(1) → **O(2^n)**
- Branching factor b, depth d → **O(b^d)**
- T(n) = T(n/2) + O(1) → **O(log n)**
- T(n) = T(n/2) + O(n) → **O(n)**

## Master theorem quick lookup
For **T(n) = aT(n/b) + O(n^k)**:
- a < b^k → **O(n^k)**
- a = b^k → **O(n^k log n)**
- a > b^k → **O(n^{log_b(a)})**

Common:
- 2T(n/2) + O(1) → **O(n)**
- 2T(n/2) + O(n) → **O(n log n)**
- T(n/2) + O(1) → **O(log n)**

## Loop shortcuts
- Single loop to n → **O(n)**
- Nested n × n → **O(n^2)**
- Nested n × m → **O(nm)**
- i *= 2 or i //= 2 → **O(log n)**
- Outer n, inner log n → **O(n log n)**

## Graph basics
- BFS / DFS → **O(V + E)**
- Dijkstra (heap) → **O((V + E) log V)**
- Union-Find amortized per op → **O(α(n))**

## Tree search patterns
- Balanced tree operations → **O(log n)**
- DFS over all nodes → **O(n)**
- Binary tree recursion visiting all nodes → **O(n)**

## DP quick rules
- DP time = **(#states) × (transition work)**
- 1D DP with O(1) transition → **O(n)**
- 2D DP n×m with O(1) transition → **O(nm)**

## Sorting and heap rules
- Sorting → **O(n log n)**
- Heap push/pop → **O(log n)**
- Build heap from array → **O(n)**

## Backtracking patterns
- All subsets → **O(2^n)**
- All permutations → **O(n!)**
- Subsets with O(n) per subset → **O(n·2^n)**
- Permutations with O(n) per permutation → **O(n·n!)**
