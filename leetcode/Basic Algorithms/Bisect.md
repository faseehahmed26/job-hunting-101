````md
https://docs.python.org/3/library/bisect.html

https://leetcode.com/discuss/general-discussion/1050071/python-bisect-module-explained-with-examples

https://www.geeksforgeeks.org/bisect-algorithms-python/

# Python bisect Module: Complete Interview Guide

The `bisect` module provides functions for maintaining **sorted lists** and performing **efficient binary search operations**. It is extremely useful in interview problems involving sorted data, range queries, and optimization.

---

## Core Concepts

### What is `bisect`?
- Built-in binary search utilities in Python
- Works on **sorted sequences**
- Search time: **O(log n)**
- Insert while maintaining order
- Avoids writing binary search repeatedly

### Key Principle
```python
import bisect

# For a sorted list a and value x:
# bisect_left(a, x)  -> leftmost position to insert x
# bisect_right(a, x) -> rightmost position to insert x
````

---

## Core Functions & Time Complexities

### 1. Search Functions

#### `bisect_left()` – Leftmost Position

```python
import bisect

arr = [1, 2, 2, 2, 3, 4, 5]
pos = bisect.bisect_left(arr, 2)  # 1
```

* Time: **O(log n)**
* Space: **O(1)**

#### `bisect_right()` / `bisect()` – Rightmost Position

```python
pos = bisect.bisect_right(arr, 2)  # 4
pos = bisect.bisect(arr, 2)        # same as bisect_right
```

---

### 2. Insertion Functions

#### `insort_left()`

```python
bisect.insort_left(arr, 2)
```

* Time: **O(n)** (array shift)
* Space: **O(1)**

#### `insort_right()` / `insort()`

```python
bisect.insort(arr, 2)
```

---

## Function Comparison Table

| Function     | Purpose                  | Time     |
| ------------ | ------------------------ | -------- |
| bisect_left  | First valid index        | O(log n) |
| bisect_right | After last valid index   | O(log n) |
| insort_left  | Insert before duplicates | O(n)     |
| insort_right | Insert after duplicates  | O(n)     |

---

## Visual Understanding

```
Array: [1, 2, 2, 2, 3, 4, 5]
Index:  0  1  2  3  4  5  6

x = 2
bisect_left  -> 1
bisect_right -> 4
```

---

## Common Patterns & Use Cases

### 1. Binary Search Using `bisect`

```python
def binary_search(arr, target):
    pos = bisect.bisect_left(arr, target)
    return pos < len(arr) and arr[pos] == target
```

---

### 2. First and Last Occurrence

```python
def find_range(arr, target):
    left = bisect.bisect_left(arr, target)
    if left == len(arr) or arr[left] != target:
        return [-1, -1]
    right = bisect.bisect_right(arr, target) - 1
    return [left, right]
```

---

### 3. Counting Occurrences

```python
def count_occurrences(arr, target):
    return bisect.bisect_right(arr, target) - bisect.bisect_left(arr, target)
```

---

### 4. Range Queries

```python
def count_in_range(arr, low, high):
    return bisect.bisect_right(arr, high) - bisect.bisect_left(arr, low)
```

---

### 5. Maintain Sorted List

```python
class SortedList:
    def __init__(self):
        self.data = []

    def add(self, val):
        bisect.insort(self.data, val)

    def remove(self, val):
        pos = bisect.bisect_left(self.data, val)
        if pos < len(self.data) and self.data[pos] == val:
            self.data.pop(pos)
```

---

## Finding Closest Elements

```python
def find_closest(arr, target):
    pos = bisect.bisect_left(arr, target)
    if pos == 0:
        return arr[0]
    if pos == len(arr):
        return arr[-1]
    return arr[pos-1] if abs(arr[pos-1]-target) <= abs(arr[pos]-target) else arr[pos]
```

---

## Advanced Patterns

### 1. Custom Objects (Python 3.10+ Key Support)

```python
pos = bisect.bisect_left(students, 87, key=lambda s: s.grade)
```

---

### 2. Coordinate Compression

```python
def coordinate_compression(coords):
    unique = sorted(set(coords))
    mapping = {v: i for i, v in enumerate(unique)}
    return [mapping[c] for c in coords]
```

---

### 3. Binary Search on Answer (Conceptual)

`bisect` logic applies even when you write binary search manually:

* Koko Eating Bananas
* Ship Packages
* Split Array Largest Sum

---

## Common Interview Problems

### Easy

* Binary Search (704)
* Search Insert Position (35)

### Medium

* Find First and Last Position (34)
* Koko Eating Bananas (875)
* Capacity to Ship Packages (1011)

### Hard

* Median of Two Sorted Arrays (4)
* Split Array Largest Sum (410)
* K-th Smallest Pair Distance (719)

---

## Common Pitfalls

### Wrong bisect choice

```python
# WRONG
bisect.bisect_right(arr, target)  # for first occurrence
```

### Bounds not checked

```python
# WRONG
arr[bisect.bisect_left(arr, x)]
```

### Too many insertions

```python
# O(n²) bad
bisect.insort(list, x)
```

---

## Quick Reference

```python
import bisect

# Existence
pos = bisect.bisect_left(arr, x)
exists = pos < len(arr) and arr[pos] == x

# Count
count = bisect.bisect_right(arr, x) - bisect.bisect_left(arr, x)

# Range
count_range = bisect.bisect_right(arr, hi) - bisect.bisect_left(arr, lo)
```

---

## Summary

The `bisect` module is essential for:

* Binary search in sorted data
* Range counting
* Insertions with order
* Replacing custom binary search logic

**Key takeaway:**
If the data is sorted and you need O(log n) search or clean boundary logic, `bisect` should be your first thought.

```
```
