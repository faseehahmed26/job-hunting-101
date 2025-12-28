````md
https://leetcode.com/problem-list/sliding-window/

https://leetcode.com/discuss/post/3722472/sliding-window-technique-a-comprehensive-ix2k/

# Sliding Window Technique: An Overview

The Sliding Window technique is an optimization method for solving problems that involve subarrays or substrings within a given array or string. Instead of recalculating results for every possible subarray, this technique maintains a "window" of elements and slides it over the input data to optimize time complexity.

---

## Key Concepts of Sliding Window

### Window
- A portion of the array or string under consideration.
- Defined by two pointers: start (beginning of the window) and end (end of the window).

### Dynamic Window
- The window size changes during traversal, depending on the problem constraints.

### Fixed Window
- The window size remains constant as it slides through the array or string.

### Sliding Mechanism
- Expand the window by moving the end pointer.
- Shrink the window by moving the start pointer when constraints are violated.

---

## Types of Problems Solved Using Sliding Window

## 1. Fixed-Size Window Problems

### Objective
Perform calculations on subarrays or substrings of a fixed size.

### Examples
- Find the maximum sum of a subarray of size k.
- Calculate the average of all subarrays of size k.

### Approach
- Expand the window until it reaches size k.
- Slide the window by removing the element at start and adding the element at end.

### Example
```python
def max_sum_subarray(nums, k):
    max_sum = 0
    window_sum = 0
    for i in range(len(nums)):
        window_sum += nums[i]
        if i >= k - 1:
            max_sum = max(max_sum, window_sum)
            window_sum -= nums[i - k + 1]
    return max_sum
````

---

## 2. Dynamic-Size Window Problems

### Objective

Adjust the window size dynamically to meet a condition.

### Examples

* Find the smallest subarray with a sum greater than or equal to a target.
* Find the longest substring with at most k distinct characters.

### Approach

* Expand the window by moving end.
* Shrink the window by moving start when constraints are violated.

### Example (Smallest Subarray with Target Sum)

```python
def min_subarray_len(target, nums):
    start = 0
    curr_sum = 0
    min_len = float('inf')
    for end in range(len(nums)):
        curr_sum += nums[end]
        while curr_sum >= target:
            min_len = min(min_len, end - start + 1)
            curr_sum -= nums[start]
            start += 1
    return min_len if min_len != float('inf') else 0
```

---

## 3. Substring Problems

### Objective

Find substrings with specific properties.

### Examples

* Longest substring with unique characters.
* Longest substring with at most k distinct characters.

### Approach

* Use a hash map to track characters and their frequencies.
* Adjust the window to satisfy constraints.

### Example (Longest Substring with At Most 2 Distinct Characters)

```python
def length_of_longest_substring(s, k):
    start = 0
    char_count = {}
    max_length = 0
    for end in range(len(s)):
        char_count[s[end]] = char_count.get(s[end], 0) + 1
        while len(char_count) > k:
            char_count[s[start]] -= 1
            if char_count[s[start]] == 0:
                del char_count[s[start]]
            start += 1
        max_length = max(max_length, end - start + 1)
    return max_length
```

---

## 4. Find Anagrams

### Objective

Find all anagrams of a pattern in a string.

### Approach

* Use a hash map to count character frequencies in the pattern.
* Maintain a sliding window of the same size as the pattern and compare frequencies.

### Example

```python
def find_anagrams(s, p):
    from collections import Counter
    p_count = Counter(p)
    s_count = Counter()
    result = []
    for i in range(len(s)):
        s_count[s[i]] += 1
        if i >= len(p):
            if s_count[s[i - len(p)]] == 1:
                del s_count[s[i - len(p)]]
            else:
                s_count[s[i - len(p)]] -= 1
        if s_count == p_count:
            result.append(i - len(p) + 1)
    return result
```

---

## 5. Maximum or Minimum in a Window

### Objective

Find the maximum or minimum element in every sliding window of size k.

### Approach

* Use a deque to store indices of useful elements.

### Example

```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()
    result = []
    for i in range(len(nums)):
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result
```

---

## 6. Count Distinct Elements in Every Window

### Objective

Count the number of distinct elements in each window of size k.

### Approach

* Use a hash map to maintain the frequency of elements.

### Example

```python
def count_distinct(nums, k):
    freq = {}
    result = []
    for i in range(len(nums)):
        freq[nums[i]] = freq.get(nums[i], 0) + 1
        if i >= k:
            freq[nums[i - k]] -= 1
            if freq[nums[i - k]] == 0:
                del freq[nums[i - k]]
        if i >= k - 1:
            result.append(len(freq))
    return result
```

---

## Techniques for Sliding Window Problems

### Expand and Contract

* Expand the window by moving end and contract by moving start based on constraints.

### Hash Map/Set for Tracking

* Track frequencies or distinct elements within the window.

### Cumulative Sum (Prefix Sum)

* Track sums within the window to avoid recomputing.

### Deque for Maximum/Minimum

* Maintain candidates for max/min efficiently.

---

## Tips for Sliding Window Problems

### Identify the Window Type

* Fixed-size: constraints depend on the size of the window.
* Dynamic-size: constraints determine when to expand or shrink the window.

### Optimize Window Adjustments

* Ensure expansions and contractions are efficient to avoid nested loops.

### Handle Edge Cases

* Empty arrays or strings.
* Window size larger than the input.

### Understand Constraints

* Define exactly when the window is valid or invalid.

### Use Hash Maps/Arrays for Efficiency

* Frequency tracking reduces repeated computation.

---

By mastering the sliding window technique and recognizing its applicability to various problem types, you will solve complex subarray and substring problems efficiently.

```
```
