# Dynamic Programming Complete Notes

## Short Notes - Quick Reference

### Core Concept

- Dynamic Programming = Recursion + Memoization
- Break problem into overlapping subproblems
- Store results to avoid recomputation
- Build solution bottom-up or top-down

### Two Approaches

#### 1. Memoization (Top-Down)
- Recursive + Cache
- dict/array stores computed results
- Start from main problem, recurse down

#### 2. Tabulation (Bottom-Up)
- Iterative + Table
- Fill table from base cases up
- No recursion overhead

### Complexity

- **Time:** `O(# of states × work per state)`
- **Space:** `O(# of states)` for table + `O(recursion depth)` for memoization

### DP Pattern Recognition

**IF problem has:**
- ✓ Optimal substructure (optimal solution uses optimal subsolutions)
- ✓ Overlapping subproblems (same subproblems solved multiple times)

**→ USE DYNAMIC PROGRAMMING**

### Common DP Dimensions

- **1D DP:** `dp[i]` - single parameter (stairs, house robber)
- **2D DP:** `dp[i][j]` - two parameters (grid, LCS, knapsack)
- **3D DP:** `dp[i][j][k]` - three parameters (knapsack variants)

### 5-Step DP Framework

1. **Define state** - What does `dp[i]` represent?
2. **Find recurrence** - How does `dp[i]` relate to previous states?
3. **Initialize base cases** - What are the simplest cases?
4. **Determine order** - Which states to compute first?
5. **Find answer** - Where is the final result?

---

## Long Notes - Comprehensive Guide

### 1. What is Dynamic Programming?

Dynamic Programming (DP) is an optimization technique that solves complex problems by:

- Breaking them into simpler overlapping subproblems
- Solving each subproblem once and storing the result
- Reusing stored results instead of recomputing

**Key Characteristics:**

- **Optimal Substructure:** Optimal solution contains optimal solutions to subproblems
- **Overlapping Subproblems:** Same subproblems are solved multiple times

**DP vs Divide & Conquer:**

- **Divide & Conquer:** Independent subproblems (e.g., Merge Sort)
- **DP:** Overlapping subproblems with shared results

---

### 2. DP Implementation Approaches

#### A. Memoization (Top-Down)

Recursive approach with caching.

**Template:**

```python
def solve_memo(n, memo={}):
    # Base case
    if n <= 1:
        return base_value
    
    # Check cache
    if n in memo:
        return memo[n]
    
    # Compute and store
    memo[n] = recurrence_relation(n)
    return memo[n]
```

**Example: Fibonacci**

```python
def fib_memo(n, memo={}):
    if n <= 1:
        return n
    if n in memo:
        return memo[n]
    memo[n] = fib_memo(n-1, memo) + fib_memo(n-2, memo)
    return memo[n]
```

**Pros:** Intuitive, only computes needed states  
**Cons:** Recursion overhead, potential stack overflow

#### B. Tabulation (Bottom-Up)

Iterative approach filling a table.

**Template:**

```python
def solve_tabulation(n):
    # Initialize table
    dp = [0] * (n + 1)
    
    # Base cases
    dp[0] = base_value_0
    dp[1] = base_value_1
    
    # Fill table
    for i in range(2, n + 1):
        dp[i] = recurrence_relation(i)
    
    return dp[n]
```

**Example: Fibonacci**

```python
def fib_tab(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[0], dp[1] = 0, 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

**Pros:** No recursion, predictable space, often faster  
**Cons:** May compute unnecessary states

---

### 3. Common DP Patterns & Problem Types

#### Pattern 1: Linear DP (1D)

**State:** `dp[i]` - answer for first i elements

**Example Problems:**
- Climbing Stairs
- House Robber
- Maximum Subarray (Kadane's)
- Decode Ways

**Climbing Stairs** - how many ways to reach step n

```python
def climbStairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]  # 1 step or 2 steps
    return dp[n]
```

**House Robber** - max money without robbing adjacent houses

```python
def rob(nums):
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    dp = [0] * len(nums)
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    for i in range(2, len(nums)):
        dp[i] = max(dp[i-1], dp[i-2] + nums[i])  # skip or rob
    return dp[-1]
```

---

#### Pattern 2: Grid DP (2D)

**State:** `dp[i][j]` - answer at position (i, j)

**Example Problems:**
- Unique Paths
- Minimum Path Sum
- Dungeon Game
- Triangle

**Unique Paths** - paths from (0,0) to (m-1,n-1)

```python
def uniquePaths(m, n):
    dp = [[0] * n for _ in range(m)]
    
    # Base cases
    for i in range(m):
        dp[i][0] = 1
    for j in range(n):
        dp[0][j] = 1
    
    # Fill table
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    
    return dp[m-1][n-1]
```

**Minimum Path Sum**

```python
def minPathSum(grid):
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    
    dp[0][0] = grid[0][0]
    
    # First row and column
    for i in range(1, m):
        dp[i][0] = dp[i-1][0] + grid[i][0]
    for j in range(1, n):
        dp[0][j] = dp[0][j-1] + grid[0][j]
    
    # Fill table
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])
    
    return dp[m-1][n-1]
```

---

#### Pattern 3: Knapsack Problems

**State:** `dp[i][w]` - max value using first i items with weight limit w

##### 0/1 Knapsack (item used once or not at all)

```python
def knapsack_01(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(1, capacity + 1):
            # Don't take item i-1
            dp[i][w] = dp[i-1][w]
            
            # Take item i-1 if possible
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], 
                              dp[i-1][w - weights[i-1]] + values[i-1])
    
    return dp[n][capacity]
```

**Space optimized (1D array)**

```python
def knapsack_01_optimized(weights, values, capacity):
    dp = [0] * (capacity + 1)
    
    for i in range(len(weights)):
        # Iterate backwards to avoid using updated values
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[capacity]
```

##### Unbounded Knapsack (unlimited items)

```python
def knapsack_unbounded(weights, values, capacity):
    dp = [0] * (capacity + 1)
    
    for w in range(1, capacity + 1):
        for i in range(len(weights)):
            if weights[i] <= w:
                dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[capacity]
```

**Knapsack Variants:**
- Subset Sum: Can we make sum S from array?
- Partition Equal Subset Sum
- Target Sum
- Coin Change
- Coin Change II (combinations)

**Coin Change** - min coins to make amount

```python
def coinChange(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1
```

**Coin Change II** - number of combinations

```python
def change(amount, coins):
    dp = [0] * (amount + 1)
    dp[0] = 1
    
    for coin in coins:  # Iterate coins first for combinations
        for i in range(coin, amount + 1):
            dp[i] += dp[i - coin]
    
    return dp[amount]
```

---

#### Pattern 4: String DP

##### Longest Common Subsequence (LCS)

**State:** `dp[i][j]` - LCS length of text1[0:i] and text2[0:j]

```python
def longestCommonSubsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]
```

##### Longest Common Substring

```python
def longestCommonSubstring(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    max_length = 0
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
                max_length = max(max_length, dp[i][j])
            else:
                dp[i][j] = 0  # Reset if mismatch
    
    return max_length
```

##### Edit Distance (Levenshtein)

```python
def minDistance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Base cases
    for i in range(m + 1):
        dp[i][0] = i  # Delete all
    for j in range(n + 1):
        dp[0][j] = j  # Insert all
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]  # No operation
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],      # Delete
                    dp[i][j-1],      # Insert
                    dp[i-1][j-1]     # Replace
                )
    
    return dp[m][n]
```

##### Longest Palindromic Subsequence

```python
def longestPalindromeSubseq(s):
    n = len(s)
    dp = [[0] * n for _ in range(n)]
    
    # Base case: single character
    for i in range(n):
        dp[i][i] = 1
    
    # Fill table diagonally
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                dp[i][j] = dp[i+1][j-1] + 2
            else:
                dp[i][j] = max(dp[i+1][j], dp[i][j-1])
    
    return dp[0][n-1]
```

**String DP Problems:**
- Longest Palindromic Substring
- Palindrome Partitioning
- Regular Expression Matching
- Wildcard Matching
- Distinct Subsequences
- Interleaving String

---

#### Pattern 5: Sequence DP

##### Longest Increasing Subsequence (LIS)

**O(n²) approach**

```python
def lengthOfLIS(nums):
    if not nums:
        return 0
    
    n = len(nums)
    dp = [1] * n  # dp[i] = LIS ending at i
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)
```

**O(n log n) approach with binary search**

```python
from bisect import bisect_left

def lengthOfLIS_fast(nums):
    sub = []
    for num in nums:
        pos = bisect_left(sub, num)
        if pos == len(sub):
            sub.append(num)
        else:
            sub[pos] = num
    return len(sub)
```

##### Maximum Sum Increasing Subsequence

```python
def maxSumIS(arr):
    n = len(arr)
    dp = arr.copy()  # dp[i] = max sum ending at i
    
    for i in range(1, n):
        for j in range(i):
            if arr[j] < arr[i]:
                dp[i] = max(dp[i], dp[j] + arr[i])
    
    return max(dp)
```

---

#### Pattern 6: Interval/Range DP

**State:** `dp[i][j]` - answer for subarray/substring from i to j

**Burst Balloons**

```python
def maxCoins(nums):
    nums = [1] + nums + [1]
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    
    # length is the size of the interval
    for length in range(2, n):
        for left in range(n - length):
            right = left + length
            for i in range(left + 1, right):
                # i is the last balloon to burst
                coins = nums[left] * nums[i] * nums[right]
                coins += dp[left][i] + dp[i][right]
                dp[left][right] = max(dp[left][right], coins)
    
    return dp[0][n-1]
```

**Matrix Chain Multiplication**

```python
def matrixChainOrder(dims):
    n = len(dims) - 1
    dp = [[0] * n for _ in range(n)]
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            for k in range(i, j):
                cost = (dp[i][k] + dp[k+1][j] + 
                       dims[i] * dims[k+1] * dims[j+1])
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[0][n-1]
```

---

#### Pattern 7: State Machine DP

Used for problems with states and transitions (e.g., buy/sell stock).

**Best Time to Buy and Sell Stock with Cooldown**

```python
def maxProfit(prices):
    if not prices:
        return 0
    
    n = len(prices)
    # States: hold stock, sold (cooldown), no stock
    hold = [0] * n
    sold = [0] * n
    rest = [0] * n
    
    hold[0] = -prices[0]
    
    for i in range(1, n):
        hold[i] = max(hold[i-1], rest[i-1] - prices[i])
        sold[i] = hold[i-1] + prices[i]
        rest[i] = max(rest[i-1], sold[i-1])
    
    return max(sold[-1], rest[-1])
```

**Best Time to Buy and Sell Stock with Transaction Fee**

```python
def maxProfit(prices, fee):
    cash = 0  # Max profit if we don't own stock
    hold = -prices[0]  # Max profit if we own stock
    
    for i in range(1, len(prices)):
        cash = max(cash, hold + prices[i] - fee)
        hold = max(hold, cash - prices[i])
    
    return cash
```

---

#### Pattern 8: Digit DP

For problems involving digit constraints.

**Count numbers with unique digits from 0 to 10^n - 1**

```python
def countNumbersWithUniqueDigits(n):
    if n == 0:
        return 1
    
    dp = [0] * (n + 1)
    dp[0] = 1
    dp[1] = 10
    
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + (dp[i-1] - dp[i-2]) * (10 - i + 1)
    
    return dp[n]
```

---

#### Pattern 9: Bitmask DP

For subset-related problems.

**Traveling Salesman Problem**

```python
def tsp(dist):
    n = len(dist)
    VISITED_ALL = (1 << n) - 1
    dp = [[float('inf')] * n for _ in range(1 << n)]
    
    dp[1][0] = 0  # Start at city 0
    
    for mask in range(1, 1 << n):
        for last in range(n):
            if not (mask & (1 << last)):
                continue
            
            for city in range(n):
                if mask & (1 << city):
                    continue
                
                new_mask = mask | (1 << city)
                dp[new_mask][city] = min(
                    dp[new_mask][city],
                    dp[mask][last] + dist[last][city]
                )
    
    return min(dp[VISITED_ALL][i] + dist[i][0] for i in range(n))
```

---

### 4. DP Optimization Techniques

#### Space Optimization

##### Rolling Array (2D → 1D)

**Before: O(n²) space**

```python
dp = [[0] * n for _ in range(n)]
```

**After: O(n) space** - only keep current and previous row

```python
prev = [0] * n
curr = [0] * n

for i in range(n):
    for j in range(n):
        curr[j] = f(prev[j], curr[j-1])  # Depends on problem
    prev, curr = curr, prev
```

##### Single Array Optimization

For knapsack-like problems, iterate backwards

```python
dp = [0] * (capacity + 1)
for i in range(n):
    for w in range(capacity, weight[i] - 1, -1):
        dp[w] = max(dp[w], dp[w - weight[i]] + value[i])
```

#### State Reduction

Instead of tracking all information, track only what's necessary.

**House Robber** - instead of dp array, track last two values

```python
def rob(nums):
    prev2, prev1 = 0, 0
    for num in nums:
        prev2, prev1 = prev1, max(prev1, prev2 + num)
    return prev1
```

---

### 5. Problem-Solving Framework

#### Step 1: Identify DP Problem

Ask these questions:
- Can the problem be broken into smaller subproblems?
- Do subproblems overlap?
- Does it involve optimization (min/max) or counting?
- Can we define it recursively?

#### Step 2: Define State

State = What information do we need to track?

**Examples:**
- `dp[i]` = answer for first i elements
- `dp[i][j]` = answer for range [i, j]
- `dp[i][j][k]` = answer with three parameters

#### Step 3: Write Recurrence Relation

How does `dp[current]` relate to previous states?

**Template:**
```
dp[i] = f(dp[i-1], dp[i-2], ..., input[i])
```

#### Step 4: Determine Base Cases

What are the simplest inputs we can solve directly?

#### Step 5: Determine Computation Order

For tabulation: Which states depend on which?  
Ensure we compute dependencies before current state

#### Step 6: Implement and Test

---

### 6. Common Mistakes & Tips

#### Mistakes to Avoid

- Off-by-one errors in array indexing
- Forgetting base cases or initializing incorrectly
- Wrong iteration order in tabulation
- Not handling edge cases (empty input, single element)
- Integer overflow for large numbers
- Infinite loops in memoization without base cases

#### Tips for Success

- Start with recursion - write naive recursive solution first
- Draw examples - visualize small cases
- Identify overlapping subproblems - where are we recomputing?
- Choose right approach - memoization for complex logic, tabulation for better space
- Test with small inputs before optimizing
- Check state transitions - verify recurrence relation
- Consider space optimization after solving correctly

---

### 7. Time & Space Complexity Guide

| Pattern | Time | Space | Example |
|---------|------|-------|---------|
| 1D DP | O(n) | O(n) → O(1) | Fibonacci, House Robber |
| 2D DP | O(n²) | O(n²) → O(n) | Grid paths, LCS |
| Knapsack | O(n×W) | O(n×W) → O(W) | 0/1 Knapsack |
| String DP | O(n×m) | O(n×m) → O(m) | Edit Distance |
| LIS (naive) | O(n²) | O(n) | LIS |
| LIS (optimal) | O(n log n) | O(n) | LIS with binary search |
| Interval DP | O(n³) | O(n²) | Burst Balloons |
| Bitmask DP | O(2ⁿ×n) | O(2ⁿ×n) | TSP |

---

### 8. Quick Reference: Problem Categories

#### Easy
- Climbing Stairs
- Min Cost Climbing Stairs
- Fibonacci Number
- Tribonacci Number
- Pascal's Triangle

#### Medium
- House Robber I, II
- Coin Change
- Longest Increasing Subsequence
- Unique Paths
- Maximum Subarray
- Jump Game
- Decode Ways
- Word Break
- Partition Equal Subset Sum

#### Hard
- Edit Distance
- Regular Expression Matching
- Wildcard Matching
- Burst Balloons
- Longest Valid Parentheses
- Interleaving String
- Distinct Subsequences
- Maximal Rectangle

---

### 9. Interview Strategy

#### Before Interview
- Master 1D and 2D DP patterns
- Practice 20-30 problems across different categories
- Understand memoization and tabulation trade-offs
- Learn common optimizations

#### During Interview
- Clarify problem - constraints, edge cases
- Try brute force first - recursive solution
- Identify overlapping subproblems - justify DP
- Define state clearly - explain to interviewer
- Write recurrence - verify logic
- Code solution - start with memoization if complex
- Test with examples - walk through small case
- Optimize if time - space reduction

#### Communication Points
- "This has optimal substructure because..."
- "I notice overlapping subproblems when..."
- "Let me define dp[i] to represent..."
- "The recurrence relation is..."
- "We can optimize space from O(n²) to O(n) by..."

---

### 10. Practice Problems by Pattern

#### 1D DP
- [ ] Climbing Stairs
- [ ] House Robber
- [ ] Maximum Subarray
- [ ] Jump Game
- [ ] Decode Ways
- [ ] Delete and Earn
- [ ] Min Cost Climbing Stairs

#### 2D Grid DP
- [ ] Unique Paths
- [ ] Unique Paths II
- [ ] Minimum Path Sum
- [ ] Maximal Square
- [ ] Dungeon Game

#### Knapsack
- [ ] 0/1 Knapsack
- [ ] Subset Sum
- [ ] Partition Equal Subset Sum
- [ ] Target Sum
- [ ] Coin Change
- [ ] Coin Change II
- [ ] Ones and Zeroes

#### String DP
- [ ] Longest Common Subsequence
- [ ] Longest Palindromic Subsequence
- [ ] Edit Distance
- [ ] Distinct Subsequences
- [ ] Palindrome Partitioning II
- [ ] Word Break
- [ ] Interleaving String

#### Sequence DP
- [ ] Longest Increasing Subsequence
- [ ] Russian Doll Envelopes
- [ ] Maximum Length of Pair Chain
- [ ] Largest Divisible Subset

#### Interval DP
- [ ] Burst Balloons
- [ ] Minimum Cost Tree From Leaf Values
- [ ] Remove Boxes
- [ ] Strange Printer

#### Stock Problems
- [ ] Best Time to Buy and Sell Stock
- [ ] Best Time to Buy and Sell Stock II
- [ ] Best Time to Buy and Sell Stock III
- [ ] Best Time to Buy and Sell Stock IV
- [ ] Best Time to Buy and Sell Stock with Cooldown
- [ ] Best Time to Buy and Sell Stock with Transaction Fee
