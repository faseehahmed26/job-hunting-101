````md
https://leetcode.com/problem-list/two-pointers/

https://leetcode.com/articles/two-pointer-technique/

https://www.reddit.com/r/leetcode/comments/18g9383/twopointer_technique_an_indepth_guide_concepts/

# Two-Pointer Technique: An Overview

The two-pointer technique is an algorithmic approach that uses two indices (or pointers) to traverse a data structure such as arrays or linked lists in a coordinated manner. It is commonly used to solve problems involving searching, sorting, pairs, subarrays, and subsequences efficiently.

---

## Key Concepts of Two-Pointer Technique

### Pointers
- Typically two variables or indices (left and right).
- Used to traverse, compare, or maintain a range in a data structure.

### Direction of Movement

#### Opposite Directions
- Pointers start from opposite ends and move toward each other.
- Common in sorted array problems.

#### Same Direction
- Both pointers move forward.
- Often used for subarray or window-based problems.

### Sliding Window
- A specialized form of two pointers.
- One pointer defines the start of the window, the other defines the end.

---

## Types of Problems Solved Using Two Pointers

## 1. Sorting and Pair Problems

### Objective
Identify or process pairs of elements that satisfy a condition such as a target sum.

### Examples
- Find two numbers in a sorted array that sum to a target.
- Check if an array has duplicates within a specific distance.

### Approach
- Start with two pointers at the beginning and end.
- Move pointers based on comparison with the target.

### Example: Two Sum (Sorted Array)
```python
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        curr_sum = arr[left] + arr[right]
        if curr_sum == target:
            return [left, right]
        elif curr_sum < target:
            left += 1
        else:
            right -= 1
    return []
````

---

## 2. Subarray Problems

### Objective

Find subarrays that satisfy a sum or length constraint.

### Examples

* Longest subarray with sum less than or equal to a target.
* Smallest subarray with sum greater than or equal to a target.

### Approach

* Expand the window with the right pointer.
* Shrink the window with the left pointer when constraints are met or violated.

### Example: Minimum Subarray Length

```python
def min_subarray_len(target, nums):
    left = 0
    curr_sum = 0
    min_len = float('inf')
    for right in range(len(nums)):
        curr_sum += nums[right]
        while curr_sum >= target:
            min_len = min(min_len, right - left + 1)
            curr_sum -= nums[left]
            left += 1
    return min_len if min_len != float('inf') else 0
```

---

## 3. Palindrome Checking

### Objective

Determine whether a string is a palindrome under given constraints.

### Examples

* Valid palindrome ignoring non-alphanumeric characters.
* Palindrome with at most one deletion.

### Approach

* Use two pointers from both ends.
* Move inward while skipping irrelevant characters.

### Example

```python
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        if s[left].lower() != s[right].lower():
            return False
        left += 1
        right -= 1
    return True
```

---

## 4. Merging Sorted Arrays

### Objective

Merge two sorted arrays into one sorted result.

### Approach

* Use one pointer for each array.
* Compare and append the smaller element.

### Example

```python
def merge_sorted_arrays(arr1, arr2):
    result = []
    i, j = 0, 0
    while i < len(arr1) and j < len(arr2):
        if arr1[i] < arr2[j]:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    return result
```

---

## 5. Finding Triplets or Quadruplets

### Objective

Identify unique combinations that meet a condition such as a target sum.

### Example

* Three Sum problem.

### Approach

* Fix one pointer.
* Apply two-pointer technique on the remaining portion.

### Example: Three Sum

```python
def three_sum(nums):
    nums.sort()
    result = []
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        left, right = i + 1, len(nums) - 1
        while left < right:
            curr_sum = nums[i] + nums[left] + nums[right]
            if curr_sum == 0:
                result.append([nums[i], nums[left], nums[right]])
                left += 1
                right -= 1
                while left < right and nums[left] == nums[left - 1]:
                    left += 1
                while left < right and nums[right] == nums[right + 1]:
                    right -= 1
            elif curr_sum < 0:
                left += 1
            else:
                right -= 1
    return result
```

---

## 6. Container Problems

### Objective

Maximize or minimize a value under constraints.

### Example

Container With Most Water.

### Approach

* Start with pointers at both ends.
* Move the pointer with the smaller height.

### Example

```python
def max_area(height):
    left, right = 0, len(height) - 1
    max_area = 0
    while left < right:
        width = right - left
        max_area = max(max_area, min(height[left], height[right]) * width)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return max_area
```

---

## 7. Intersection of Two Arrays

### Objective

Find common elements between two sorted arrays.

### Approach

* Traverse both arrays with two pointers.
* Move pointers based on comparison.

### Example

```python
def intersect(nums1, nums2):
    nums1.sort()
    nums2.sort()
    i, j = 0, 0
    result = []
    while i < len(nums1) and j < len(nums2):
        if nums1[i] == nums2[j]:
            result.append(nums1[i])
            i += 1
            j += 1
        elif nums1[i] < nums2[j]:
            i += 1
        else:
            j += 1
    return result
```

---

## Tips for Solving Two-Pointer Problems

* Identify whether pointers move in the same or opposite direction.
* Sort the input if required.
* Move pointers deliberately based on condition checks.
* Handle duplicates carefully.
* Always consider edge cases such as empty or single-element inputs.

---

## Advantages of the Two-Pointer Technique

* Reduces time complexity compared to brute force.
* Easy to reason about and implement.
* Extremely versatile across many problem categories.

---

By mastering the two-pointer technique and recognizing its patterns, you will be able to solve a wide range of array, string, and linked list problems efficiently in interviews.

```
```
