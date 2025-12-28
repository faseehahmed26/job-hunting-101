
# Arrays and Hashing: An Overview

Arrays and hashing are foundational concepts in computer science and programming. Arrays are simple data structures that store elements in contiguous memory locations, while hashing uses hash functions to map data to a hash table for efficient lookups and retrievals.

---

## Arrays: Key Concepts

### Definition
An array is a collection of elements identified by an index or key.

**Example**
```python
arr = [10, 20, 30]  # indexes: 0, 1, 2
````

### Operations

* **Access:** `O(1)` time complexity for direct index access
* **Search:**

  * `O(n)` in an unsorted array
  * `O(log n)` in a sorted array using binary search
* **Insertion / Deletion:** Generally `O(n)` because elements may need to be shifted

### Variants

* **Fixed-Size Arrays:** Predefined size, common in languages like C and C++
* **Dynamic Arrays:** Can grow or shrink dynamically, for example Python lists

---

## Hashing: Key Concepts

### Definition

A hash table is a data structure that stores key-value pairs.
Hashing uses a hash function to map keys to indices in the hash table.

### Hash Function

Converts input (key) into a fixed-size value (hash code).

**Example**

```text
hash("key") -> 42
```

### Operations

* **Insertion / Search / Deletion:** Average `O(1)` due to direct indexing

### Collision Handling

* **Chaining:** Store multiple values at the same index using a linked list
* **Open Addressing:** Probe for the next available slot

---

## Common Problems and Solutions Using Arrays and Hashing

### 1. Frequency Count

**Objective:** Count the frequency of elements in an array.

**Approach:**
Use a hash map (dictionary) to store counts.

```python
def count_frequency(nums):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1
    return freq
```

---

### 2. Two Sum

**Objective:** Find two numbers in an array that sum to a target.

**Approach:**
Use a hash map to store visited elements and their indices.

```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        diff = target - num
        if diff in seen:
            return [seen[diff], i]
        seen[num] = i
    return []
```

---

### 3. Subarray with Given Sum

**Objective:** Find a subarray that sums to a target.

**Approach:**
Use a hash map to store cumulative sums and their indices.

```python
def subarray_sum(nums, target):
    prefix_sum = 0
    seen = {0: -1}
    for i, num in enumerate(nums):
        prefix_sum += num
        if prefix_sum - target in seen:
            return (seen[prefix_sum - target] + 1, i)
        seen[prefix_sum] = i
    return None
```

---

### 4. Longest Subarray with Equal 0s and 1s

**Objective:** Find the longest subarray with an equal number of 0s and 1s.

**Approach:**
Treat 0 as -1 and use cumulative sum with a hash map.

```python
def find_max_length(nums):
    prefix_sum = 0
    seen = {0: -1}
    max_length = 0
    for i, num in enumerate(nums):
        prefix_sum += 1 if num == 1 else -1
        if prefix_sum in seen:
            max_length = max(max_length, i - seen[prefix_sum])
        else:
            seen[prefix_sum] = i
    return max_length
```

---

### 5. Intersection of Two Arrays

**Objective:** Find the intersection of two arrays.

**Approach:**
Use a hash set for one array and check membership with the other.

```python
def array_intersection(nums1, nums2):
    set1 = set(nums1)
    return [num for num in nums2 if num in set1]
```

---

### 6. Longest Consecutive Sequence

**Objective:** Find the length of the longest consecutive sequence.

**Approach:**
Use a hash set and iterate only on sequence starters.

```python
def longest_consecutive(nums):
    nums_set = set(nums)
    max_length = 0
    for num in nums_set:
        if num - 1 not in nums_set:
            length = 1
            while num + length in nums_set:
                length += 1
            max_length = max(max_length, length)
    return max_length
```

---

### 7. Check for Duplicates

**Objective:** Determine if an array contains duplicates.

**Approach:**
Use a hash set to track seen elements.

```python
def contains_duplicates(nums):
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False
```

---

### 8. Group Anagrams

**Objective:** Group strings that are anagrams.

**Approach:**
Use a hash map with the sorted string as the key.

```python
def group_anagrams(strs):
    anagrams = {}
    for s in strs:
        key = ''.join(sorted(s))
        anagrams.setdefault(key, []).append(s)
    return list(anagrams.values())
```

---

### 9. Find Majority Element

**Objective:** Find the element that appears more than `n / 2` times.

**Approach:**

* Use a hash map to count occurrences
* Or use the Boyer-Moore Voting Algorithm for `O(n)` time and `O(1)` space

```python
def majority_element(nums):
    count, candidate = 0, None
    for num in nums:
        if count == 0:
            candidate = num
        count += 1 if num == candidate else -1
    return candidate
```

---

## Techniques for Solving Array and Hashing Problems

### 1. Hash Map or Set for Fast Lookups

Use a hash map or set for constant-time lookups.
Examples include Two Sum and Frequency Count.

### 2. Sliding Window

Efficiently solve subarray or substring problems by maintaining a window.
Example: Longest subarray problems.

### 3. Prefix Sum

Use cumulative sums to simplify range queries or subarray problems.
Example: Subarray with Given Sum.

### 4. Sorting and Binary Search

Sort the array and use binary search or two pointers.
Examples include Two Sum in a sorted array and triplet problems.

### 5. Custom Hashing

Design custom hash functions for complex keys like tuples or strings.
Example: Group Anagrams.

---

## Tips for Array and Hashing Problems

* **Analyze Problem Requirements:**

  * Finding, counting, or grouping elements often benefits from hashing
  * Problems requiring sorted order benefit from arrays and sorting

* **Handle Edge Cases:**
  Empty arrays, single-element arrays, and duplicate values

* **Optimize Space and Time:**

  * Prefer in-place array operations when possible
  * Use hash maps for constant-time lookups

* **Iterate Efficiently:**
  Reduce nested loops by using hashing, prefix sums, or two-pointer techniques

---

By mastering arrays and hashing techniques, you will be well prepared to tackle a wide variety of coding challenges efficiently and effectively.

