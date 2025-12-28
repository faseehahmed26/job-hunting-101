````md
https://leetcode.com/discuss/post/3726061/binary-search-a-comprehensive-guide-by-i-3nxx/

https://leetcode.com/discuss/post/1263403/python-powerful-ultimate-binary-search-template-solved-many-problems/

# Binary Search: Complete Interview Guide

Binary search is a powerful technique that reduces search time from O(n) to O(log n).  
The core insight is **monotonicity**: if a condition is true at some point, it stays true (or false) beyond that point.

---

## Core Concepts

### What is Binary Search?
- Repeatedly divide the search space in half
- Keep the half that can still contain the answer
- Discard the half that cannot
- Works on any **monotonic condition**, not only sorted arrays

### When to Use Binary Search?
- You can define a condition `condition(x)` that is monotonic
- You want to **minimize x** such that `condition(x)` is true
- You want to **maximize x** such that `condition(x)` is true
- Direct brute force is too slow

---

## Universal Binary Search Template

```python
def binary_search(search_space):
    def condition(value) -> bool:
        # return True if value is acceptable
        pass

    left, right = min(search_space), max(search_space)
    while left < right:
        mid = left + (right - left) // 2
        if condition(mid):
            right = mid
        else:
            left = mid + 1
    return left
````

### Template Rules

* Boundaries must include all possible answers
* `condition(mid) == True` means search left
* Loop invariant: `left < right`
* Returned value is the **minimum valid answer**

---

## Problem Categories and Patterns

## 1. Classic Array Search Problems

### Find Target in Sorted Array

```python
def search_target(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] >= target:
            right = mid
        else:
            left = mid + 1
    return left if left < len(nums) and nums[left] == target else -1
```

### Find Insert Position

```python
def search_insert(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] >= target:
            right = mid
        else:
            left = mid + 1
    return left
```

### First and Last Occurrence

```python
def find_first(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] >= target:
            right = mid
        else:
            left = mid + 1
    return left if left < len(nums) and nums[left] == target else -1

def find_last(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > target:
            right = mid
        else:
            left = mid + 1
    return left - 1 if left > 0 and nums[left - 1] == target else -1
```

---

## 2. Peak Finding Problems

### Find Peak Element

```python
def find_peak(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > nums[mid + 1]:
            right = mid
        else:
            left = mid + 1
    return left
```

---

## 3. Rotated Sorted Array

### Search in Rotated Sorted Array

```python
def search_rotated(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1
```

### Find Minimum in Rotated Array

```python
def find_min(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid
    return nums[left]
```

---

## 4. Mathematical Binary Search

### Integer Square Root

```python
def my_sqrt(x):
    left, right = 0, x + 1
    while left < right:
        mid = left + (right - left) // 2
        if mid * mid > x:
            right = mid
        else:
            left = mid + 1
    return left - 1
```

---

## 5. Binary Search on Answer (Optimization)

### Ship Packages Within D Days

```python
def ship_within_days(weights, days):
    def can_ship(capacity):
        curr, d = 0, 1
        for w in weights:
            if curr + w > capacity:
:
                d += 1
                curr = w
                if d > days:
                    return False
            else:
                curr += w
        return True

    left, right = max(weights), sum(weights)
    while left < right:
        mid = left + (right - left) // 2
        if can_ship(mid):
            right = mid
        else:
            left = mid + 1
    return left
```

### Koko Eating Bananas

```python
def min_eating_speed(piles, h):
    def can_finish(speed):
        hours = 0
        for p in piles:
            hours += (p + speed - 1) // speed
        return hours <= h

    left, right = 1, max(piles)
    while left < right:
        mid = left + (right - left) // 2
        if can_finish(mid):
            right = mid
        else:
            left = mid + 1
    return left
```

---

## 6. Kth Element Problems

### Kth Smallest Number in Multiplication Table

```python
def find_kth(m, n, k):
    def enough(x):
        count = 0
        for i in range(1, m + 1):
            count += min(x // i, n)
        return count >= k

    left, right = 1, m * n
    while left < right:
        mid = left + (right - left) // 2
        if enough(mid):
            right = mid
        else:
            left = mid + 1
    return left
```

---

## Binary Search Variations

### Find Maximum Valid Value

```python
def binary_search_max(left, right):
    def condition(x):
        pass

    while left < right:
        mid = left + (right - left + 1) // 2
        if condition(mid):
            left = mid
        else:
            right = mid - 1
    return left
```

### Floating Point Binary Search

```python
def binary_search_float(left, right, eps=1e-6):
    def condition(x):
        pass

    while right - left > eps:
        mid = (left + right) / 2
        if condition(mid):
            right = mid
        else:
            left = mid
    return left
```

---

## Interview Recognition Signals

### Keywords

* Minimum or maximum value
* Kth smallest or largest
* First or last occurrence
* Capacity, speed, time, distance
* Optimize under constraints

### Characteristics

* Search space can be defined
* Monotonic property exists
* Checking feasibility is easier than finding answer

---

## Common Mistakes

* Using `left <= right` when template needs `left < right`
* Incorrect mid calculation
* Off by one boundaries
* Forgetting to include all valid answers

---

## Time and Space Complexity

* Time: O(log n)
* Total time: O(log n × check)
* Space: O(1) iterative

---

## Practice Problems

### Easy

* Binary Search (704)
* Search Insert Position (35)
* Sqrt(x) (69)

### Medium

* Find Peak Element (162)
* Search in Rotated Sorted Array (33)
* Koko Eating Bananas (875)

### Hard

* Split Array Largest Sum (410)
* Kth Smallest Number in Multiplication Table (668)
* Median of Two Sorted Arrays (4)

---

**Key Takeaway**
Binary search is about finding boundaries in a monotonic space, not just searching sorted arrays.

```
```
