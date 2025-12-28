````md
# Prefix Sum: Complete Interview Guide

Prefix sum transforms **O(n)** range queries into **O(1)** operations by preprocessing the array once in **O(n)** time.

Core idea: store cumulative sums so subarray sums can be answered instantly.

---

## Core Concepts

### What is Prefix Sum?
- Preprocess array to store cumulative sums
- Query any subarray sum in **O(1)** time
- Trade space for speed: **O(n)** space, **O(1)** queries
- Formula:  
  **sum(i, j) = prefix[j + 1] - prefix[i]**

### When to Use Prefix Sum?
- Multiple range sum queries on the same array
- Subarray sum counting problems
- Cumulative or running totals
- Problems involving continuous ranges

---

## Universal Prefix Sum Template

```python
def build_prefix_sum(nums):
    n = len(nums)
    prefix = [0] * (n + 1)

    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]

    return prefix

def range_sum(prefix, left, right):
    return prefix[right + 1] - prefix[left]
````

### Template Rules

* Always create prefix array of size `n + 1`
* `prefix[0] = 0` simplifies logic
* `prefix[i]` stores sum of first `i` elements
* Range sum is `prefix[r + 1] - prefix[l]`

---

## Problem Categories and Patterns

## 1. Basic Range Sum Queries

### Static Range Queries

```python
class NumArray:
    def __init__(self, nums):
        self.prefix = [0]
        for num in nums:
            self.prefix.append(self.prefix[-1] + num)

    def sumRange(self, left, right):
        return self.prefix[right + 1] - self.prefix[left]
```

---

## 2. Subarray Sum Problems

### Count Subarrays With Target Sum

```python
def subarray_sum(nums, k):
    count = 0
    prefix_sum = 0
    sum_freq = {0: 1}

    for num in nums:
        prefix_sum += num
        if prefix_sum - k in sum_freq:
            count += sum_freq[prefix_sum - k]
        sum_freq[prefix_sum] = sum_freq.get(prefix_sum, 0) + 1

    return count
```

**Key Insight:**
If `prefix[j] - prefix[i] = k`, then subarray `(i+1 ... j)` sums to `k`.

---

### Maximum Subarray Sum (Kadane)

```python
def max_subarray_sum(nums):
    max_sum = current_sum = nums[0]

    for num in nums[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)

    return max_sum
```

---

## 3. 2D Prefix Sum (Matrix)

### Build 2D Prefix Sum

```python
def build_2d_prefix(matrix):
    if not matrix:
        return []

    rows, cols = len(matrix), len(matrix[0])
    prefix = [[0] * (cols + 1) for _ in range(rows + 1)]

    for i in range(1, rows + 1):
        for j in range(1, cols + 1):
            prefix[i][j] = (
                matrix[i-1][j-1]
                + prefix[i-1][j]
                + prefix[i][j-1]
                - prefix[i-1][j-1]
            )

    return prefix
```

### Query 2D Range Sum

```python
def range_sum_2d(prefix, r1, c1, r2, c2):
    return (
        prefix[r2+1][c2+1]
        - prefix[r1][c2+1]
        - prefix[r2+1][c1]
        + prefix[r1][c1]
    )
```

**Memory Trick:**
Add top and left, subtract top-left (counted twice), add current.

---

## 4. Prefix Sum with HashMap

### Subarrays With Sum Divisible by K

```python
def subarraysDivByK(nums, k):
    prefix_sum = 0
    count = 0
    remainder_freq = {0: 1}

    for num in nums:
        prefix_sum += num
        remainder = prefix_sum % k
        if remainder < 0:
            remainder += k

        count += remainder_freq.get(remainder, 0)
        remainder_freq[remainder] = remainder_freq.get(remainder, 0) + 1

    return count
```

---

## 5. Multiple Prefix and Suffix Sums

### Product of Array Except Self

```python
def product_except_self(nums):
    n = len(nums)
    result = [1] * n

    left = 1
    for i in range(n):
        result[i] = left
        left *= nums[i]

    right = 1
    for i in range(n - 1, -1, -1):
        result[i] *= right
        right *= nums[i]

    return result
```

---

## 6. Prefix XOR

### XOR Range Queries

```python
def xor_queries(arr, queries):
    prefix_xor = [0]
    for num in arr:
        prefix_xor.append(prefix_xor[-1] ^ num)

    res = []
    for l, r in queries:
        res.append(prefix_xor[r + 1] ^ prefix_xor[l])

    return res
```

---

## 7. Difference Array (Inverse Prefix Sum)

### Range Update Queries

```python
def range_updates(n, updates):
    diff = [0] * (n + 1)

    for start, end, val in updates:
        diff[start] += val
        diff[end + 1] -= val

    res = []
    curr = 0
    for i in range(n):
        curr += diff[i]
        res.append(curr)

    return res
```

---

## Advanced Patterns

## 1. Prefix Sum + Binary Search

### Shortest Subarray With Sum ≥ K

```python
def shortest_subarray(nums, k):
    n = len(nums)
    prefix = [0]
    for num in nums:
        prefix.append(prefix[-1] + num)

    min_len = float("inf")

    for i in range(n + 1):
        target = prefix[i] + k
        left, right = i + 1, n

        while left <= right:
            mid = (left + right) // 2
            if prefix[mid] >= target:
                min_len = min(min_len, mid - i)
                right = mid - 1
            else:
                left = mid + 1

    return min_len if min_len != float("inf") else -1
```

---

## 2. Prefix Sum + Sliding Window

### Maximum Sum Subarray of Size K

```python
def max_sum_subarray_k(nums, k):
    window_sum = sum(nums[:k])
    max_sum = window_sum

    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)

    return max_sum
```

---

## 3. Prefix Count (Sorted Insert)

```python
def count_smaller(nums):
    from bisect import insort, bisect_left

    seen = []
    res = []

    for i in range(len(nums) - 1, -1, -1):
        pos = bisect_left(seen, nums[i])
        res.append(pos)
        insort(seen, nums[i])

    return res[::-1]
```

---

## Common Tricks and Optimizations

### Space Optimization

```python
def running_sum(nums):
    for i in range(1, len(nums)):
        nums[i] += nums[i - 1]
    return nums
```

### Modular Prefix Sum

```python
def prefix_sum_mod(nums, mod):
    prefix = [0]
    for num in nums:
        prefix.append((prefix[-1] + num) % mod)
    return prefix
```

---

## Interview Problem Identification

### Keywords

* Subarray sum
* Range sum query
* Cumulative sum
* Count subarrays
* Maximum or minimum subarray

### Characteristics

* Multiple queries on same array
* Subarray-based calculations
* O(n²) brute force too slow

---

## Common Mistakes

* Off-by-one indexing errors
* Forgetting `prefix[0] = 0`
* Mishandling empty arrays
* Incorrect handling of negative numbers

---

## Time and Space Complexity

| Operation    | Time     | Space |
| ------------ | -------- | ----- |
| Build prefix | O(n)     | O(n)  |
| Range query  | O(1)     | -     |
| m queries    | O(n + m) | O(n)  |

---

## Practice Problems

### Easy

* Range Sum Query (303)
* Running Sum (1480)
* Find Pivot Index (724)

### Medium

* Subarray Sum Equals K (560)
* Product Except Self (238)
* Subarrays Divisible by K (974)
* Range Sum Query 2D (304)

### Hard

* Shortest Subarray ≥ K (862)
* Count of Range Sum (327)
* Max Sum of 3 Subarrays (689)

---

## Quick Reference

```python
# Build prefix
prefix = [0]
for x in arr:
    prefix.append(prefix[-1] + x)

# Range sum [i, j]
sum_ij = prefix[j + 1] - prefix[i]
```

**Remember:**
Prefix sum is about preprocessing once to answer many queries fast. One O(n) pass saves countless O(n) computations.

```
```
