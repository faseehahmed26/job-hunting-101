
---

The Dynamic Programming Interview Cheat Sheet

1. The 1D DP (Fibonacci Style)

Use this when: You need to count the number of ways to reach a target, or find a minimum cost to reach a target, where the current state depends strictly on the previous 1 or 2 states.

The Concept:
Instead of a recursion tree (which recalculates the same values), we build an array (or just use two variables) from the bottom up.

$F(n) = F(n-1) + F(n-2)$

Code Template (Space Optimized):
This template solves Fibonacci, Climbing Stairs, and House Robber.

Python

```python
def solve_1d_dp(n):
    if n <= 1: return n
    
    # prev2 is dp[i-2], prev1 is dp[i-1]
    prev2, prev1 = 0, 1
    
    for i in range(2, n + 1):
        # The recurrence relation changes based on the problem
        # Standard Fib: current = prev1 + prev2
        # Cost Climbing Stairs: current = cost[i] + min(prev1, prev2)
        current = prev1 + prev2 
        
        # Shift pointers for next iteration
        prev2 = prev1
        prev1 = current
        
    return prev1
```

Complexity:
Time: $O(n)$
Space: $O(1)$ (because we only store two variables, not the whole array).

---

2. The 0/1 Knapsack

Use this when: You have a list of items, each with a weight and a value. You have a maximum capacity. You must maximize value strictly deciding Take It or Leave It (cannot split items, cannot take same item twice).

The Concept:
We build a 2D Grid.
Rows: The items.
Cols: The capacity (0 to Max).
Cell dp[i][c]: Max profit using first i items with capacity c.

The Logic:
Skip Item: Value is same as the cell directly above (previous items, same capacity).
Take Item: Value is current item's value + value of the cell in previous row at (current_capacity - item_weight).

Code Template:

Python

```python
def solve_knapsack(profit, weight, capacity):
    N = len(profit)
    # dp[N+1][M+1] initialized to 0
    # Rows = Items, Cols = Capacity
    dp = [[0] * (capacity + 1) for _ in range(N + 1)]

    for i in range(1, N + 1):
        for c in range(1, capacity + 1):
            
            # The item we are currently considering
            curr_profit = profit[i-1]
            curr_weight = weight[i-1]
            
            if curr_weight <= c:
                # Max of (Skip it, Take it)
                # Skip: dp[i-1][c] -> Look Up
                # Take: curr_profit + dp[i-1][c - curr_weight] -> Look Up & Left
                dp[i][c] = max(dp[i-1][c], curr_profit + dp[i-1][c - curr_weight])
            else:
                # Too heavy, must skip
                dp[i][c] = dp[i-1][c]
                
    return dp[N][capacity]
```

Complexity:
Time: $O(N * C)$ (Number of items * Capacity)
Space: $O(N * C)$ (Can be optimized to $O(C)$ using a 1D array).

---

3. Unbounded Knapsack

Use this when: Similar to 0/1 Knapsack, but you have an infinite supply of each item. (e.g., Coin Change, Rod Cutting).

The Concept:
Since we can reuse items, we do not need to look at the "previous row" (items considered so far). We only need to look at the "current row" or a simple 1D array representing the target sum.

The Logic:
dp[i] = Best value for capacity i.
To solve for capacity i, we try every possible item or coin.

Code Template:

Python

```python
def solve_unbounded(capacity, items):
    # Initialize dp array. 
    # For MAX value problems, init with 0.
    # For MIN way problems (like min coins), init with infinity.
    dp = [0] * (capacity + 1)
    # Base case (usually dp[0] = 0 or 1 depending on problem)
    dp[0] = 0 

    # Iterate through every capacity from 1 to target
    for c in range(1, capacity + 1):
        for item_val in items:
            if c - item_val >= 0:
                # Recurrence:
                # current = max(current, item_val + dp[remainder])
                dp[c] = max(dp[c], item_val + dp[c - item_val])
                
    return dp[capacity]
```

Complexity:
Time: $O(Capacity * Items)$
Space: $O(Capacity)$

---

4. Longest Common Subsequence (LCS)

Use this when: You are comparing two strings or sequences and need to find optimal matching (longest match, min edits to match, etc.).

The Concept:
A 2D Grid where:
Row: String A
Col: String B
Diagonal Move: Characters match! (Add 1 to result).
Right or Down Move: Characters do not match (Inherit best result from neighbors).

Code Template:

Python

```python
def solve_lcs(text1, text2):
    n, m = len(text1), len(text2)
    # Grid size (n+1) x (m+1)
    dp = [[0] * (m + 1) for _ in range(n + 1)]

    # Iterate backwards (or forwards, both work, backwards is common in NeetCode)
    for i in range(n - 1, -1, -1):
        for j in range(m - 1, -1, -1):
            
            if text1[i] == text2[j]:
                # Match found: 1 + result of removing both chars (diagonal)
                dp[i][j] = 1 + dp[i + 1][j + 1]
            else:
                # No match: Max of removing char from text1 OR text2
                dp[i][j] = max(dp[i + 1][j], dp[i][j + 1])
                
    return dp[0][0]
```

Complexity:
Time: $O(N * M)$
Space: $O(N * M)$

---

5. Palindromes (Expand From Center)

Use this when: You need to find the longest palindromic substring or count palindromic substrings.

The Concept:
Do not use a DP grid. Instead, treat every character (and every gap between characters) as the "center" of a mirror. Expand outwards left and right while the characters match.

The Logic:
Iterate i through the string.
Odd Length: l, r = i, i (Center is one char). Expand.
Even Length: l, r = i, i + 1 (Center is gap). Expand.

Code Template:

Python

```python
def count_palindromes(s):
    count = 0
    
    for i in range(len(s)):
        # Check Odd Length Palindromes
        l, r = i, i
        while l >= 0 and r < len(s) and s[l] == s[r]:
            count += 1 # Found a palindrome
            l -= 1
            r += 1
            
        # Check Even Length Palindromes
        l, r = i, i + 1
        while l >= 0 and r < len(s) and s[l] == s[r]:
            count += 1 # Found a palindrome
            l -= 1
            r += 1
            
    return count
```

Complexity:
Time: $O(N^2)$ (Better than the Naive $O(N^3)$).
Space: $O(1)$ (No storage required).

---

Quick Reference Summary Table

Pattern
Key Identifier
State
Recurrence
Complexity

Fibonacci
Count ways or steps (Jump 1 or 2)
dp[i]
dp[i] = dp[i-1] + dp[i-2]
T: $O(N)$ S: $O(1)$

0/1 Knapsack
Max value, limited capacity, use once
dp[i][c]
max(skip, take + dp[i-1][rem])
T: $O(N \cdot C)$ S: $O(N \cdot C)$

Unbounded
Max value, limited capacity, reuse items
dp[c]
max(skip, take + dp[c - item])
T: $O(N \cdot C)$ S: $O(C)$

LCS
Compare 2 strings, order matters
dp[i][j]
Match: 1+diag

No Match: max(up, left)
T: $O(N \cdot M)$ S: $O(N \cdot M)$

Palindrome
Substrings that reverse
Pointers
Expand l-- and r++ while s[l]==s[r]
T: $O(N^2)$ S: $O(1)$

---

