# 20 Dynamic Programming Patterns - Complete Interview Guide

## Pattern 1: Fibonacci Sequence

### When to Use
- Problem depends on solutions of smaller instances
- Clear recursive relationship: `F(n) = F(n-1) + F(n-2)`
- Counting ways to reach a state
- Each state depends on fixed number of previous states

### Why to Use
- Eliminates redundant calculations through memoization
- Transforms exponential time to linear/polynomial time
- Natural fit for recurrence relations

### Core Intuition
Think of climbing stairs: to reach step `n`, you must come from step `n-1` or `n-2`. The total ways to reach `n` is the sum of ways to reach those previous steps.

### Brute Force Approach
**Idea**: Use pure recursion without storing results
```python
def fib_brute(n):
    if n <= 1:
        return n
    return fib_brute(n-1) + fib_brute(n-2)
```
**TC**: O(2^n) - exponential growth  
**SC**: O(n) - recursion stack depth

### DP Template
```python
# Bottom-Up (Tabulation)
def fibonacci(n):
    if n <= 1:
        return n
    
    dp = [0] * (n + 1)
    dp[1] = 1
    
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    
    return dp[n]

# Space Optimized
def fibonacci_optimized(n):
    if n <= 1:
        return n
    
    prev2, prev1 = 0, 1
    for i in range(2, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr
    
    return prev1
```
**TC**: O(n)  
**SC**: O(n) for tabulation, O(1) for optimized

---

## Pattern 2: Kadane's Algorithm

### When to Use
- Finding maximum/minimum sum of contiguous subarray
- Optimizing over consecutive elements
- Problems with "maximum/minimum subarray" keywords

### Why to Use
- Efficiently handles arrays with negative numbers
- Single pass solution
- Can be modified for various subarray problems

### Core Intuition
At each position, decide: should I extend the previous subarray or start fresh? Keep the choice that gives maximum sum. Track the global maximum throughout.

### Brute Force Approach
**Idea**: Check all possible subarrays
```python
def max_subarray_brute(nums):
    max_sum = float('-inf')
    for i in range(len(nums)):
        current_sum = 0
        for j in range(i, len(nums)):
            current_sum += nums[j]
            max_sum = max(max_sum, current_sum)
    return max_sum
```
**TC**: O(n²)  
**SC**: O(1)

### DP Template
```python
def kadane(nums):
    max_ending_here = max_so_far = nums[0]
    
    for i in range(1, len(nums)):
        # Either extend previous subarray or start new
        max_ending_here = max(nums[i], max_ending_here + nums[i])
        max_so_far = max(max_so_far, max_ending_here)
    
    return max_so_far

# With subarray indices
def kadane_with_indices(nums):
    max_sum = current_sum = nums[0]
    start = end = temp_start = 0
    
    for i in range(1, len(nums)):
        if current_sum < 0:
            current_sum = nums[i]
            temp_start = i
        else:
            current_sum += nums[i]
        
        if current_sum > max_sum:
            max_sum = current_sum
            start = temp_start
            end = i
    
    return max_sum, start, end
```
**TC**: O(n)  
**SC**: O(1)

---

## Pattern 3: 0/1 Knapsack

### When to Use
- Set of items with weights and values
- Constraint on total capacity
- Each item used once (include/exclude decision)
- Maximize/minimize total value

### Why to Use
- Foundation for many DP problems
- Handles binary choice problems elegantly
- Can be extended to various constraint problems

### Core Intuition
For each item, make a choice: take it (if capacity allows) or leave it. The optimal solution considers both possibilities and picks the better one.

### Brute Force Approach
**Idea**: Try all 2^n combinations
```python
def knapsack_brute(weights, values, capacity, n):
    if n == 0 or capacity == 0:
        return 0
    
    # If weight exceeds capacity, skip item
    if weights[n-1] > capacity:
        return knapsack_brute(weights, values, capacity, n-1)
    
    # Max of including or excluding current item
    include = values[n-1] + knapsack_brute(weights, values, 
                                           capacity - weights[n-1], n-1)
    exclude = knapsack_brute(weights, values, capacity, n-1)
    
    return max(include, exclude)
```
**TC**: O(2^n)  
**SC**: O(n) - recursion stack

### DP Template
```python
def knapsack_01(weights, values, capacity):
    n = len(weights)
    # dp[i][w] = max value using first i items with capacity w
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(1, capacity + 1):
            # Don't take item i-1
            dp[i][w] = dp[i-1][w]
            
            # Take item i-1 if possible
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], 
                              values[i-1] + dp[i-1][w - weights[i-1]])
    
    return dp[n][capacity]

# Space Optimized - 1D DP
def knapsack_01_optimized(weights, values, capacity):
    dp = [0] * (capacity + 1)
    
    for i in range(len(weights)):
        # Traverse backwards to avoid using same item twice
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
    
    return dp[capacity]
```
**TC**: O(n × capacity)  
**SC**: O(n × capacity) for 2D, O(capacity) for 1D

---

## Pattern 4: Unbounded Knapsack

### When to Use
- Similar to 0/1 knapsack but items can be used multiple times
- Unlimited supply of each item
- Coin change, rod cutting problems

### Why to Use
- Handles infinite supply scenarios
- Slightly different iteration than 0/1 knapsack

### Core Intuition
Unlike 0/1 knapsack, after taking an item, you can take it again. This means when we take item `i`, we stay at row `i` instead of moving to `i-1`.

### Brute Force Approach
**Idea**: Recursively try taking each item multiple times
```python
def unbounded_knapsack_brute(weights, values, capacity, n):
    if capacity == 0 or n == 0:
        return 0
    
    if weights[n-1] > capacity:
        return unbounded_knapsack_brute(weights, values, capacity, n-1)
    
    # Include: stay at same item (can use again)
    include = values[n-1] + unbounded_knapsack_brute(
        weights, values, capacity - weights[n-1], n)
    exclude = unbounded_knapsack_brute(weights, values, capacity, n-1)
    
    return max(include, exclude)
```
**TC**: O(2^n) - worse due to repetition  
**SC**: O(n)

### DP Template
```python
def unbounded_knapsack(weights, values, capacity):
    dp = [0] * (capacity + 1)
    
    for i in range(len(weights)):
        # Traverse forward (can reuse same item)
        for w in range(weights[i], capacity + 1):
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
    
    return dp[capacity]

# 2D version for clarity
def unbounded_knapsack_2d(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(1, capacity + 1):
            dp[i][w] = dp[i-1][w]  # Exclude
            
            if weights[i-1] <= w:
                # Include: use dp[i] (same row) not dp[i-1]
                dp[i][w] = max(dp[i][w], 
                              values[i-1] + dp[i][w - weights[i-1]])
    
    return dp[n][capacity]
```
**TC**: O(n × capacity)  
**SC**: O(capacity) for 1D, O(n × capacity) for 2D

---

## Pattern 5: Longest Common Subsequence (LCS)

### When to Use
- Two sequences given, find common pattern
- Subsequence (not necessarily contiguous)
- Order must be preserved
- Keywords: "common", "both strings"

### Why to Use
- Foundation for diff algorithms
- String similarity problems
- Edit distance problems

### Core Intuition
If characters match, they can be part of LCS. If they don't match, LCS is the maximum of excluding one character from either string.

### Brute Force Approach
**Idea**: Try all subsequences of both strings
```python
def lcs_brute(s1, s2, m, n):
    if m == 0 or n == 0:
        return 0
    
    if s1[m-1] == s2[n-1]:
        return 1 + lcs_brute(s1, s2, m-1, n-1)
    
    return max(lcs_brute(s1, s2, m-1, n), 
               lcs_brute(s1, s2, m, n-1))
```
**TC**: O(2^(m+n))  
**SC**: O(m+n) - recursion depth

### DP Template
```python
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    # dp[i][j] = LCS length of s1[0:i] and s2[0:j]
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]

# With backtracking to find actual LCS
def lcs_with_string(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    # Backtrack to find LCS
    i, j = m, n
    lcs_str = []
    while i > 0 and j > 0:
        if s1[i-1] == s2[j-1]:
            lcs_str.append(s1[i-1])
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
    
    return dp[m][n], ''.join(reversed(lcs_str))
```
**TC**: O(m × n)  
**SC**: O(m × n)

---

## Pattern 6: Longest Increasing Subsequence (LIS)

### When to Use
- Find longest subsequence with increasing elements
- Elements must maintain relative order
- Not necessarily contiguous
- Can be modified for strictly/non-strictly increasing

### Why to Use
- Optimization problems with ordering constraints
- Stock price problems
- Scheduling problems

### Core Intuition
For each element, find the longest increasing subsequence ending at that element by checking all previous elements smaller than it.

### Brute Force Approach
**Idea**: Try all subsequences
```python
def lis_brute(arr, n, prev):
    if n == 0:
        return 0
    
    # Exclude current element
    exclude = lis_brute(arr, n-1, prev)
    
    # Include if possible
    include = 0
    if prev == -1 or arr[n-1] > arr[prev]:
        include = 1 + lis_brute(arr, n-1, n-1)
    
    return max(include, exclude)
```
**TC**: O(2^n)  
**SC**: O(n)

### DP Template
```python
# O(n²) DP Solution
def lis_dp(nums):
    if not nums:
        return 0
    
    n = len(nums)
    # dp[i] = length of LIS ending at index i
    dp = [1] * n
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)

# O(n log n) Binary Search Solution
def lis_binary_search(nums):
    from bisect import bisect_left
    
    # tails[i] = smallest tail of all increasing subsequences of length i+1
    tails = []
    
    for num in nums:
        pos = bisect_left(tails, num)
        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num
    
    return len(tails)

# With actual LIS reconstruction
def lis_with_sequence(nums):
    n = len(nums)
    dp = [1] * n
    parent = [-1] * n
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i] and dp[j] + 1 > dp[i]:
                dp[i] = dp[j] + 1
                parent[i] = j
    
    # Find index with max length
    max_len = max(dp)
    max_idx = dp.index(max_len)
    
    # Reconstruct LIS
    lis = []
    idx = max_idx
    while idx != -1:
        lis.append(nums[idx])
        idx = parent[idx]
    
    return max_len, list(reversed(lis))
```
**TC**: O(n²) for DP, O(n log n) for binary search  
**SC**: O(n)

---

## Pattern 7: Palindromic Subsequence

### When to Use
- Finding palindromes in strings
- Subsequences that read same forwards/backwards
- Minimum operations to make palindrome

### Why to Use
- String manipulation problems
- Optimization with palindrome constraints

### Core Intuition
If first and last characters match, they can be part of palindrome. If not, try removing one and find best palindrome in remaining string.

### Brute Force Approach
**Idea**: Check all subsequences for palindrome property
```python
def lps_brute(s, i, j):
    if i == j:
        return 1
    if i > j:
        return 0
    
    if s[i] == s[j]:
        return 2 + lps_brute(s, i+1, j-1)
    
    return max(lps_brute(s, i+1, j), lps_brute(s, i, j-1))
```
**TC**: O(2^n)  
**SC**: O(n)

### DP Template
```python
def longest_palindromic_subsequence(s):
    n = len(s)
    # dp[i][j] = length of LPS in s[i:j+1]
    dp = [[0] * n for _ in range(n)]
    
    # Every single character is a palindrome of length 1
    for i in range(n):
        dp[i][i] = 1
    
    # Fill table for substrings of length 2 to n
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            
            if s[i] == s[j]:
                dp[i][j] = 2 + dp[i+1][j-1]
            else:
                dp[i][j] = max(dp[i+1][j], dp[i][j-1])
    
    return dp[0][n-1]

# Count palindromic substrings (contiguous)
def count_palindromic_substrings(s):
    n = len(s)
    dp = [[False] * n for _ in range(n)]
    count = 0
    
    # Single characters
    for i in range(n):
        dp[i][i] = True
        count += 1
    
    # Two characters
    for i in range(n - 1):
        if s[i] == s[i+1]:
            dp[i][i+1] = True
            count += 1
    
    # Longer substrings
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j] and dp[i+1][j-1]:
                dp[i][j] = True
                count += 1
    
    return count
```
**TC**: O(n²)  
**SC**: O(n²)

---

## Pattern 8: Edit Distance

### When to Use
- Transform one string to another
- Operations: insert, delete, substitute
- Minimum number of operations needed
- String similarity metrics

### Why to Use
- Spell checkers, DNA sequencing
- Diff tools
- String comparison algorithms

### Core Intuition
At each position, you have three choices: insert, delete, or replace a character. Pick the option that gives minimum total operations.

### Brute Force Approach
**Idea**: Try all three operations recursively
```python
def edit_distance_brute(s1, s2, m, n):
    if m == 0:
        return n
    if n == 0:
        return m
    
    if s1[m-1] == s2[n-1]:
        return edit_distance_brute(s1, s2, m-1, n-1)
    
    insert = edit_distance_brute(s1, s2, m, n-1)
    delete = edit_distance_brute(s1, s2, m-1, n)
    replace = edit_distance_brute(s1, s2, m-1, n-1)
    
    return 1 + min(insert, delete, replace)
```
**TC**: O(3^(m+n))  
**SC**: O(m+n)

### DP Template
```python
def edit_distance(s1, s2):
    m, n = len(s1), len(s2)
    # dp[i][j] = min operations to convert s1[0:i] to s2[0:j]
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Base cases
    for i in range(m + 1):
        dp[i][0] = i  # Delete all characters
    for j in range(n + 1):
        dp[0][j] = j  # Insert all characters
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],    # Delete from s1
                    dp[i][j-1],    # Insert into s1
                    dp[i-1][j-1]   # Replace in s1
                )
    
    return dp[m][n]

# Space optimized
def edit_distance_optimized(s1, s2):
    m, n = len(s1), len(s2)
    prev = list(range(n + 1))
    
    for i in range(1, m + 1):
        curr = [i] + [0] * n
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                curr[j] = prev[j-1]
            else:
                curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
        prev = curr
    
    return prev[n]
```
**TC**: O(m × n)  
**SC**: O(m × n) for 2D, O(n) for optimized

---

## Pattern 9: Subset Sum

### When to Use
- Determine if subset sums to target
- Partition problems
- Combination sum with constraints

### Why to Use
- Foundation for partition problems
- Variant of 0/1 knapsack
- Binary choice per element

### Core Intuition
For each number, decide to include it or not. If included, reduce target by that amount. Build up solutions for all possible sums.

### Brute Force Approach
**Idea**: Try all 2^n subsets
```python
def subset_sum_brute(nums, target, n):
    if target == 0:
        return True
    if n == 0:
        return False
    
    # Exclude current element
    if nums[n-1] > target:
        return subset_sum_brute(nums, target, n-1)
    
    # Include or exclude
    return (subset_sum_brute(nums, target - nums[n-1], n-1) or
            subset_sum_brute(nums, target, n-1))
```
**TC**: O(2^n)  
**SC**: O(n)

### DP Template
```python
def subset_sum(nums, target):
    n = len(nums)
    # dp[i][j] = can we make sum j using first i elements
    dp = [[False] * (target + 1) for _ in range(n + 1)]
    
    # Base case: sum 0 is always possible
    for i in range(n + 1):
        dp[i][0] = True
    
    for i in range(1, n + 1):
        for j in range(1, target + 1):
            # Exclude current element
            dp[i][j] = dp[i-1][j]
            
            # Include if possible
            if nums[i-1] <= j:
                dp[i][j] = dp[i][j] or dp[i-1][j - nums[i-1]]
    
    return dp[n][target]

# Space optimized
def subset_sum_optimized(nums, target):
    dp = [False] * (target + 1)
    dp[0] = True
    
    for num in nums:
        # Traverse backwards
        for j in range(target, num - 1, -1):
            dp[j] = dp[j] or dp[j - num]
    
    return dp[target]

# Find all possible sums
def all_possible_sums(nums):
    dp = {0}
    
    for num in nums:
        new_sums = set()
        for s in dp:
            new_sums.add(s + num)
        dp.update(new_sums)
    
    return dp
```
**TC**: O(n × target)  
**SC**: O(n × target) for 2D, O(target) for 1D

---

## Pattern 10: String Partition

### When to Use
- Splitting string into valid parts
- Dictionary-based problems (Word Break)
- Minimize partitions with conditions
- Segment string optimally

### Why to Use
- NLP problems
- String validation
- Optimization with substring constraints

### Core Intuition
Try all possible ways to partition the string. For each position, check if we can form a valid partition up to that point using previous results.

### Brute Force Approach
**Idea**: Try all possible partitions recursively
```python
def word_break_brute(s, word_dict, start=0):
    if start == len(s):
        return True
    
    for end in range(start + 1, len(s) + 1):
        if s[start:end] in word_dict and word_break_brute(s, word_dict, end):
            return True
    
    return False
```
**TC**: O(2^n)  
**SC**: O(n)

### DP Template
```python
def word_break(s, word_dict):
    n = len(s)
    word_set = set(word_dict)
    # dp[i] = can we break s[0:i] into words
    dp = [False] * (n + 1)
    dp[0] = True  # Empty string
    
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]

# Minimum cuts for palindrome partitioning
def min_palindrome_cuts(s):
    n = len(s)
    # is_palindrome[i][j] = is s[i:j+1] a palindrome
    is_palindrome = [[False] * n for _ in range(n)]
    
    # Build palindrome table
    for i in range(n):
        is_palindrome[i][i] = True
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                is_palindrome[i][j] = (length == 2 or is_palindrome[i+1][j-1])
    
    # dp[i] = min cuts needed for s[0:i]
    dp = [float('inf')] * n
    
    for i in range(n):
        if is_palindrome[0][i]:
            dp[i] = 0
        else:
            for j in range(i):
                if is_palindrome[j+1][i]:
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n-1]
```
**TC**: O(n²) for word break, O(n³) for palindrome cuts  
**SC**: O(n)

---

## Pattern 11: Catalan Numbers

### When to Use
- Counting valid parentheses combinations
- Number of BSTs with n nodes
- Triangulation of polygons
- Path counting problems with restrictions

### Why to Use
- Recursive counting with symmetry
- Combinatorial optimization
- Structural counting problems

### Core Intuition
Catalan numbers count structures that can be recursively divided. C(n) = sum of C(i) × C(n-1-i) for all i from 0 to n-1.

### Brute Force Approach
**Idea**: Direct recursive formula
```python
def catalan_brute(n):
    if n <= 1:
        return 1
    
    result = 0
    for i in range(n):
        result += catalan_brute(i) * catalan_brute(n - 1 - i)
    
    return result
```
**TC**: O(4^n / n^1.5) - exponential  
**SC**: O(n)

### DP Template
```python
def catalan_number(n):
    # dp[i] = ith Catalan number
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            dp[i] += dp[j] * dp[i-1-j]
    
    return dp[n]

# Count unique BSTs
def num_unique_bst(n):
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for nodes in range(2, n + 1):
        for root in range(1, nodes + 1):
            left = root - 1
            right = nodes - root
            dp[nodes] += dp[left] * dp[right]
    
    return dp[n]

# Generate valid parentheses
def generate_parentheses(n):
    def backtrack(s, left, right):
        if len(s) == 2 * n:
            result.append(s)
            return
        
        if left < n:
            backtrack(s + '(', left + 1, right)
        if right < left:
            backtrack(s + ')', left, right + 1)
    
    result = []
    backtrack('', 0, 0)
    return result
```
**TC**: O(n²) for nth Catalan  
**SC**: O(n)

---

## Pattern 12: Matrix Chain Multiplication

### When to Use
- Optimal order of operations
- Cost depends on operation order
- Partitioning with cost optimization
- Keywords: "minimize cost", "optimal way to combine"

### Why to Use
- Classic interval DP problem
- Applies to many optimization scenarios
- Burst balloons, polygon triangulation

### Core Intuition
Try all possible ways to split the sequence. For each split point, recursively solve left and right parts, then combine with split cost.

### Brute Force Approach
**Idea**: Try all possible split points
```python
def mcm_brute(dims, i, j):
    if i >= j:
        return 0
    
    min_cost = float('inf')
    for k in range(i, j):
        cost = (mcm_brute(dims, i, k) +
                mcm_brute(dims, k+1, j) +
                dims[i-1] * dims[k] * dims[j])
        min_cost = min(min_cost, cost)
    
    return min_cost
```
**TC**: O(2^n)  
**SC**: O(n)

### DP Template
```python
def matrix_chain_order(dims):
    n = len(dims) - 1  # Number of matrices
    # dp[i][j] = min cost to multiply matrices i to j
    dp = [[0] * n for _ in range(n)]
    
    # length is chain length - 1
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            for k in range(i, j):
                cost = (dp[i][k] + dp[k+1][j] + 
                       dims[i] * dims[k+1] * dims[j+1])
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[0][n-1]

# Burst Balloons pattern
def max_coins(nums):
    # Add boundaries
    nums = [1] + nums + [1]
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    
    for length in range(2, n):
        for left in range(n - length):
            right = left + length
            for k in range(left + 1, right):
                # k is last balloon to burst in range (left, right)
                coins = nums[left] * nums[k] * nums[right]
                dp[left][right] = max(dp[left][right],
                                     dp[left][k] + dp[k][right] + coins)
    
    return dp[0][n-1]
```
**TC**: O(n³)  
**SC**: O(n²)

---

## Pattern 13: Count Distinct Ways

### When to Use
- Count number of ways to achieve goal
- Multiple valid paths/combinations
- "How many ways" problems
- Decode ways, staircase variations

### Why to Use
- Combinatorial counting
- Optimization with counting constraint
- Path counting in constraints

### Core Intuition
At each step, count ways by summing ways from all valid previous states. Build up total count from base cases.

### Brute Force Approach
**Idea**: Enumerate all valid sequences
```python
def count_ways_brute(s, index=0):
    if index == len(s):
        return 1
    if s[index] == '0':
        return 0
    
    # Decode 1 digit
    ways = count_ways_brute(s, index + 1)
    
    # Decode 2 digits if valid
    if index + 1 < len(s) and int(s[index:index+2]) <= 26:
        ways += count_ways_brute(s, index + 2)
    
    return ways
```
**TC**: O(2^n)  
**SC**: O(n)

### DP Template
```python
# Decode Ways
def decode_ways(s):
    if not s or s[0] == '0':
        return 0
    
    n = len(s)
    # dp[i] = number of ways to decode s[0:i]
    dp = [0] * (n + 1)
    dp[0] = 1  # Empty string
    dp[1] = 1  # First character
    
    for i in range(2, n + 1):
        # Single digit decode
        if s[i-1] != '0':
            dp[i] += dp[i-1]
        
        # Two digit decode
        two_digit = int(s[i-2:i])
        if 10 <= two_digit <= 26:
            dp[i] += dp[i-2]
    
    return dp[n]

# Climbing stairs with k steps
def climb_stairs_k_steps(n, k):
    # dp[i] = ways to reach step i
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            dp[i] += dp[i - j]
    
    return dp[n]

# Space optimized for k steps
def climb_stairs_optimized(n, k):
    dp = [0] * k
    dp[0] = 1
    
    for i in range(1, n + 1):
        temp = sum(dp)
        for j in range(k - 1, 0, -1):
            dp[j] = dp[j-1]
        dp[0] = temp
    
    return dp[0]
```
**TC**: O(n) for decode ways, O(n × k) for k-step stairs  
**SC**: O(n)

---

## Pattern 14: DP on Grids

### When to Use
- 2D array/matrix traversal
- Path counting or optimization
- Move in specific directions
- Obstacles or constraints in grid

### Why to Use
- Natural 2D DP representation
- Robot navigation, pathfinding
- Matrix optimization problems

### Core Intuition
Each cell's value depends on neighboring cells. Build solution from top-left to bottom-right, considering valid moves.

### Brute Force Approach
**Idea**: Try all paths recursively
```python
def unique_paths_brute(m, n, i=0, j=0):
    if i == m - 1 and j == n - 1:
        return 1
    if i >= m or j >= n:
        return 0
    
    # Move right or down
    return (unique_paths_brute(m, n, i+1, j) +
            unique_paths_brute(m, n, i, j+1))
```
**TC**: O(2^(m+n))  
**SC**: O(m+n)

### DP Template
```python
# Unique Paths
def unique_paths(m, n):
    # dp[i][j] = number of paths to reach (i, j)
    dp = [[1] * n for _ in range(m)]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    
    return dp[m-1][n-1]

# Minimum Path Sum
def min_path_sum(grid):
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = grid[0][0]
    
    # Initialize first row and column
    for i in range(1, m):
        dp[i][0] = dp[i-1][0] + grid[i][0]
    for j in range(1, n):
        dp[0][j] = dp[0][j-1] + grid[0][j]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])
    
    return dp[m-1][n-1]

# Space Optimized
def min_path_sum_optimized(grid):
    m, n = len(grid), len(grid[0])
    dp = grid[0][:]
    
    for j in range(1, n):
        dp[j] += dp[j-1]
    
    for i in range(1, m):
        dp[0] += grid[i][0]
        for j in range(1, n):
            dp[j] = grid[i][j] + min(dp[j], dp[j-1])
    
    return dp[n-1]

# With obstacles
def unique_paths_with_obstacles(grid):
    m, n = len(grid), len(grid[0])
    if grid[0][0] == 1 or grid[m-1][n-1] == 1:
        return 0
    
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = 1
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1:
                dp[i][j] = 0
            elif i > 0 or j > 0:
                dp[i][j] = (dp[i-1][j] if i > 0 else 0) + \
                          (dp[i][j-1] if j > 0 else 0)
    
    return dp[m-1][n-1]
```
**TC**: O(m × n)  
**SC**: O(m × n) or O(n) optimized

---

## Pattern 15: DP on Trees

### When to Use
- Binary tree problems
- Optimization over tree structure
- Root-to-leaf or subtree problems
- House Robber, Max Path Sum variants

### Why to Use
- Tree structure naturally fits recursion
- Subproblem solutions at child nodes
- Post-order traversal pattern

### Core Intuition
Solve for each subtree independently. Combine results from left and right children with current node's contribution.

### Brute Force Approach
**Idea**: Try all possibilities at each node
```python
def rob_tree_brute(root):
    if not root:
        return 0
    
    # Rob current node
    rob_current = root.val
    if root.left:
        rob_current += rob_tree_brute(root.left.left) + \
                      rob_tree_brute(root.left.right)
    if root.right:
        rob_current += rob_tree_brute(root.right.left) + \
                       rob_tree_brute(root.right.right)
    
    # Skip current node
    skip_current = rob_tree_brute(root.left) + rob_tree_brute(root.right)
    
    return max(rob_current, skip_current)
```
**TC**: O(2^n)  
**SC**: O(h) where h is height

### DP Template
```python
# House Robber III
def rob_tree(root):
    def dfs(node):
        if not node:
            return (0, 0)  # (rob, not_rob)
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        # Rob current node: can't rob children
        rob = node.val + left[1] + right[1]
        
        # Don't rob current: take max from children
        not_rob = max(left) + max(right)
        
        return (rob, not_rob)
    
    return max(dfs(root))

# Binary Tree Maximum Path Sum
def max_path_sum(root):
    max_sum = float('-inf')
    
    def dfs(node):
        nonlocal max_sum
        if not node:
            return 0
        
        # Only consider positive contributions
        left = max(0, dfs(node.left))
        right = max(0, dfs(node.right))
        
        # Path through current node
        current_path = node.val + left + right
        max_sum = max(max_sum, current_path)
        
        # Return max path extending to parent
        return node.val + max(left, right)
    
    dfs(root)
    return max_sum

# Minimum cameras to monitor tree
def min_camera_cover(root):
    NOT_MONITORED, MONITORED, HAS_CAMERA = 0, 1, 2
    cameras = 0
    
    def dfs(node):
        nonlocal cameras
        if not node:
            return MONITORED
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        # If any child not monitored, place camera here
        if left == NOT_MONITORED or right == NOT_MONITORED:
            cameras += 1
            return HAS_CAMERA
        
        # If any child has camera, this is monitored
        if left == HAS_CAMERA or right == HAS_CAMERA:
            return MONITORED
        
        # Both children monitored but no camera, this needs monitoring
        return NOT_MONITORED
    
    if dfs(root) == NOT_MONITORED:
        cameras += 1
    
    return cameras
```
**TC**: O(n) where n is number of nodes  
**SC**: O(h) for recursion stack

---

## Pattern 16: DP on Graphs

### When to Use
- Graph traversal with optimization
- Shortest/longest path variants
- State-dependent graph problems
- Paths with constraints (K stops)

### Why to Use
- Handles complex state spaces
- Combines DP with graph algorithms
- Constraint-based optimization

### Core Intuition
Extend Dijkstra's or BFS with DP state. Track additional dimension (remaining moves, visited nodes) along with distance.

### Brute Force Approach
**Idea**: DFS with all paths
```python
def cheapest_flight_brute(n, flights, src, dst, k, stops=0):
    if src == dst:
        return 0
    if stops > k:
        return float('inf')
    
    min_cost = float('inf')
    for u, v, price in flights:
        if u == src:
            cost = price + cheapest_flight_brute(n, flights, v, dst, k, stops+1)
            min_cost = min(min_cost, cost)
    
    return min_cost
```
**TC**: O(V^K) where V is vertices  
**SC**: O(K)

### DP Template
```python
# Cheapest Flights Within K Stops (Bellman-Ford DP)
def find_cheapest_price(n, flights, src, dst, k):
    # dp[i] = min cost to reach each city
    dp = [float('inf')] * n
    dp[src] = 0
    
    # Relax edges k+1 times
    for _ in range(k + 1):
        temp = dp[:]
        for u, v, price in flights:
            if dp[u] != float('inf'):
                temp[v] = min(temp[v], dp[u] + price)
        dp = temp
    
    return dp[dst] if dp[dst] != float('inf') else -1

# With Dijkstra + DP
def find_cheapest_price_dijkstra(n, flights, src, dst, k):
    from heapq import heappush, heappop
    from collections import defaultdict
    
    graph = defaultdict(list)
    for u, v, price in flights:
        graph[u].append((v, price))
    
    # (cost, city, stops)
    heap = [(0, src, 0)]
    visited = {}
    
    while heap:
        cost, city, stops = heappop(heap)
        
        if city == dst:
            return cost
        
        if stops > k:
            continue
        
        if city in visited and visited[city] <= stops:
            continue
        visited[city] = stops
        
        for neighbor, price in graph[city]:
            heappush(heap, (cost + price, neighbor, stops + 1))
    
    return -1

# Shortest path visiting all nodes (TSP variant)
def shortest_path_all_nodes(graph):
    n = len(graph)
    # dp[mask][i] = min distance to visit nodes in mask, ending at i
    dp = [[float('inf')] * n for _ in range(1 << n)]
    
    for i in range(n):
        dp[1 << i][i] = 0
    
    for mask in range(1 << n):
        for i in range(n):
            if dp[mask][i] == float('inf'):
                continue
            for j in graph[i]:
                new_mask = mask | (1 << j)
                dp[new_mask][j] = min(dp[new_mask][j], 
                                     dp[mask][i] + 1)
    
    final_mask = (1 << n) - 1
    return min(dp[final_mask])
```
**TC**: O(E × K) for flights, O(2^n × n²) for TSP variant  
**SC**: O(n × K) for flights, O(2^n × n) for TSP

---

## Pattern 17: Digit DP

### When to Use
- Counting numbers in range [L, R]
- Digit-based constraints
- Large ranges (10^18)
- Properties based on digit patterns

### Why to Use
- Avoids iterating all numbers
- Handles huge ranges efficiently
- Digit-by-digit construction

### Core Intuition
Build numbers digit by digit. Track if we're still bounded by the limit and what constraints are satisfied so far.

### Brute Force Approach
**Idea**: Check each number in range
```python
def count_digits_brute(n):
    count = 0
    for i in range(1, n + 1):
        if has_unique_digits(i):
            count += 1
    return count

def has_unique_digits(num):
    seen = set()
    while num:
        digit = num % 10
        if digit in seen:
            return False
        seen.add(digit)
        num //= 10
    return True
```
**TC**: O(n × log n)  
**SC**: O(1)

### DP Template
```python
# Count numbers with unique digits
def count_numbers_unique_digits(n):
    if n == 0:
        return 1
    if n == 1:
        return 10
    
    # First digit: 9 choices (1-9)
    # Second digit: 9 choices (0-9 except first)
    # Third digit: 8 choices, etc.
    result = 10  # Single digit numbers
    available_digits = 9
    unique_digits = 9
    
    for i in range(2, n + 1):
        unique_digits *= available_digits
        result += unique_digits
        available_digits -= 1
    
    return result

# Digit DP template: Count numbers <= n with property
def digit_dp(num_str):
    n = len(num_str)
    memo = {}
    
    def dp(pos, tight, started, state):
        # pos: current digit position
        # tight: still bounded by num_str
        # started: has number started (handle leading zeros)
        # state: problem-specific state
        
        if pos == n:
            return int(started and check_property(state))
        
        key = (pos, tight, started, state)
        if key in memo:
            return memo[key]
        
        limit = int(num_str[pos]) if tight else 9
        result = 0
        
        for digit in range(0, limit + 1):
            new_started = started or (digit != 0)
            new_tight = tight and (digit == limit)
            new_state = update_state(state, digit, new_started)
            
            result += dp(pos + 1, new_tight, new_started, new_state)
        
        memo[key] = result
        return result
    
    return dp(0, True, False, initial_state())

````md
## Pattern 17: Digit DP (finish the example)

### DP Template Example: Count number of digit `1` in `[1, n]`
This is a classic “count by position” digit DP idea (count contributions of each digit place).

```python
def count_digit_one(n: int) -> int:
    if n <= 0:
        return 0

    count = 0
    factor = 1  # 1, 10, 100, ...
    while factor <= n:
        lower = n % factor
        curr = (n // factor) % 10
        higher = n // (factor * 10)

        if curr == 0:
            count += higher * factor
        elif curr == 1:
            count += higher * factor + (lower + 1)
        else:
            count += (higher + 1) * factor

        factor *= 10

    return count
````

**TC**: `O(log10(n))`
**SC**: `O(1)`

---

## Pattern 18: Bitmasking DP

### When to Use

* You must consider subsets of items (pick, visit, assign, schedule)
* `n` is small (usually `n ≤ 20`, sometimes `≤ 25`)
* You want “best result for a chosen set so far”
* Keywords: “visit all”, “use each once”, “minimum over subsets”, “assignment”, “tour”

### Why to Use

* A subset can be stored as an integer bitmask
* DP can iterate over all subsets efficiently: `2^n` states

### Core Intuition

Represent a set of chosen elements using bits.
`mask` encodes what is already taken, and DP stores the best value for that `mask` (sometimes also the “current position”).

### Brute Force Approach

**Idea**: Try every permutation / assignment (explodes quickly)

* TSP brute force: `O(n!)`

### DP Template (TSP / “visit all nodes”)

`dp[mask][i] = min cost to visit nodes in mask and end at node i`

```python
from math import inf

def tsp_min_cost(dist):
    n = len(dist)
    dp = [[inf] * n for _ in range(1 << n)]

    # Start at 0 (common convention)
    dp[1 << 0][0] = 0

    for mask in range(1 << n):
        for i in range(n):
            if dp[mask][i] == inf:
                continue
            if (mask & (1 << i)) == 0:
                continue
            for j in range(n):
                if mask & (1 << j):
                    continue
                new_mask = mask | (1 << j)
                dp[new_mask][j] = min(dp[new_mask][j], dp[mask][i] + dist[i][j])

    full = (1 << n) - 1

    # If you must return to start:
    ans = inf
    for i in range(n):
        ans = min(ans, dp[full][i] + dist[i][0])

    return ans
```

**TC**: `O(2^n * n^2)`
**SC**: `O(2^n * n)`

---

## Pattern 19: Probability DP

### When to Use

* Random process with steps (moves, turns, draws)
* You need probability of being in a state after `k` steps
* Or expected value computed from previous probabilities
* Keywords: “probability”, “expected”, “random”, “chance after k moves”

### Why to Use

* The probability of a state is a sum of probabilities of prior states times transition probabilities
* Recomputing paths is expensive; DP keeps it clean and fast

### Core Intuition

Let `dp[step][state]` be the probability of being at `state` after `step` transitions.
Then update using transitions from previous step.

### Brute Force Approach

**Idea**: DFS all random paths (branching factor grows exponentially)
**TC**: `O(b^k)` for branching factor `b`
**SC**: `O(k)`

### DP Template Example (Knight Probability in Chessboard)

`dp[r][c] = probability of being at (r, c) after current step`

```python
def knight_probability(n: int, k: int, row: int, col: int) -> float:
    moves = [
        (2, 1), (2, -1), (-2, 1), (-2, -1),
        (1, 2), (1, -2), (-1, 2), (-1, -2)
    ]

    dp = [[0.0] * n for _ in range(n)]
    dp[row][col] = 1.0

    for _ in range(k):
        nxt = [[0.0] * n for _ in range(n)]
        for r in range(n):
            for c in range(n):
                if dp[r][c] == 0.0:
                    continue
                p = dp[r][c] / 8.0
                for dr, dc in moves:
                    nr, nc = r + dr, c + dc
                    if 0 <= nr < n and 0 <= nc < n:
                        nxt[nr][nc] += p
        dp = nxt

    return sum(sum(row_vals) for row_vals in dp)
```

**TC**: `O(k * n^2)`
**SC**: `O(n^2)`

---

## Pattern 20: State Machine DP

### When to Use

* You have a sequence of decisions over time
* Your “mode” matters (holding, not holding, cooldown, used transactions, etc.)
* Keywords: “buy/sell”, “cooldown”, “at most k transactions”, “must rest”, “switching states”

### Why to Use

* Forces you to model valid transitions only
* Prevents missing constraints (cooldowns, limited transactions)

### Core Intuition

Define a small set of states and transitions.
DP just simulates the best value of each state at each time step.

### Brute Force Approach

**Idea**: Try all buy/sell decisions recursively
**TC**: exponential
**SC**: `O(n)` recursion depth

### DP Template Example (Best Time to Buy and Sell Stock with Cooldown)

States:

* `hold`: max profit when you currently hold a stock
* `sold`: max profit when you sold today (so tomorrow is cooldown)
* `rest`: max profit when you do not hold and you are not in cooldown

Transitions each day with price `p`:

* `new_hold = max(hold, rest - p)`
* `new_sold = hold + p`
* `new_rest = max(rest, sold)`

```python
def max_profit_with_cooldown(prices):
    if not prices:
        return 0

    hold = -prices[0]
    sold = float("-inf")
    rest = 0

    for p in prices[1:]:
        new_hold = max(hold, rest - p)
        new_sold = hold + p
        new_rest = max(rest, sold)

        hold, sold, rest = new_hold, new_sold, new_rest

    return max(rest, sold)
```

**TC**: `O(n)`
**SC**: `O(1)`

```
```
