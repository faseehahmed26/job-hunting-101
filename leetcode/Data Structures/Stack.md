```md
# Monotonic Stack: Complete Interview Guide

A **monotonic stack** maintains elements in increasing or decreasing order, enabling `O(n)` solutions for problems that would otherwise require `O(n²)` time.

**Key insight:**  
When an element is popped from the stack, its next greater or smaller element has been found.

---

## Core Concepts

### What Is a Monotonic Stack?
- A stack that maintains elements in sorted order (increasing or decreasing).
- Pop elements that violate the monotonic property.
- Each element is pushed and popped exactly once.
- Overall time complexity is `O(n)`.
- Ideal for finding nearest greater or smaller elements.

### When to Use a Monotonic Stack
- Next or previous greater or smaller element problems.
- Range or span problems.
- Histogram or rectangle problems.
- Temperature, stock span, or visibility problems.
- Any problem asking: *“How far until you see X?”*

---

## The Four Fundamental Patterns

### Pattern Decision Tree

```

Need element on which side?
├─ LEFT (Previous)
│  ├─ Iterate: Left → Right
│  ├─ Append result immediately
│  └─ Smaller? Pop >= | Greater? Pop <=
│
└─ RIGHT (Next)
├─ Iterate: Right → Left
├─ Store result in array
└─ Smaller? Pop >= | Greater? Pop <=

````

---

## 1️⃣ Previous Smaller Element (PSE)

**Use case:** Nearest smaller element to the left.

```python
def previousSmaller(nums):
    stack = []
    res = []

    for x in nums:
        while stack and stack[-1] >= x:
            stack.pop()

        res.append(stack[-1] if stack else -1)
        stack.append(x)

    return res
````

**Example**

```
Input:  [4, 5, 2, 10, 8]
Output: [-1, 4, -1, 2, 2]
```

---

## 2️⃣ Next Smaller Element (NSE)

**Use case:** Nearest smaller element to the right.

```python
def nextSmaller(nums):
    stack = []
    res = [-1] * len(nums)

    for i in range(len(nums) - 1, -1, -1):
        while stack and stack[-1] >= nums[i]:
            stack.pop()

        res[i] = stack[-1] if stack else -1
        stack.append(nums[i])

    return res
```

**Example**

```
Input:  [4, 5, 2, 10, 8]
Output: [2, 2, -1, 8, -1]
```

---

## 3️⃣ Previous Greater Element (PGE)

**Use case:** Nearest greater element to the left.

```python
def previousGreater(nums):
    stack = []
    res = []

    for x in nums:
        while stack and stack[-1] <= x:
            stack.pop()

        res.append(stack[-1] if stack else -1)
        stack.append(x)

    return res
```

**Example**

```
Input:  [4, 5, 2, 10, 8]
Output: [-1, -1, 5, -1, 10]
```

---

## 4️⃣ Next Greater Element (NGE)

**Use case:** Nearest greater element to the right.

```python
def nextGreater(nums):
    stack = []
    res = [-1] * len(nums)

    for i in range(len(nums) - 1, -1, -1):
        while stack and stack[-1] <= nums[i]:
            stack.pop()

        res[i] = stack[-1] if stack else -1
        stack.append(nums[i])

    return res
```

**Example**

```
Input:  [4, 5, 2, 10, 8]
Output: [5, 10, 10, -1, -1]
```

---

## Pattern Variations

### Storing Indices Instead of Values

Use indices when:

* Distances are required.
* Original array reference is needed.
* Multiple operations depend on positions.

```python
def nextGreaterIndices(nums):
    stack = []
    res = [-1] * len(nums)

    for i in range(len(nums) - 1, -1, -1):
        while stack and nums[stack[-1]] <= nums[i]:
            stack.pop()

        res[i] = stack[-1] if stack else -1
        stack.append(i)

    return res
```

### Storing Tuples (Value, Index)

```python
def nextGreaterWithIndices(nums):
    stack = []
    res = [-1] * len(nums)

    for i in range(len(nums) - 1, -1, -1):
        while stack and stack[-1][0] <= nums[i]:
            stack.pop()

        res[i] = stack[-1] if stack else (-1, -1)
        stack.append((nums[i], i))

    return res
```

---

## Problem Categories

### 1. Classic Next and Previous Element Problems

#### Daily Temperatures

```python
def dailyTemperatures(temperatures):
    stack = []
    res = [0] * len(temperatures)

    for i in range(len(temperatures)):
        while stack and temperatures[stack[-1]] < temperatures[i]:
            prev = stack.pop()
            res[prev] = i - prev
        stack.append(i)

    return res
```

#### Next Greater Element I

```python
def nextGreaterElement(nums1, nums2):
    nge = {}
    stack = []

    for num in nums2:
        while stack and stack[-1] < num:
            nge[stack.pop()] = num
        stack.append(num)

    return [nge.get(num, -1) for num in nums1]
```

#### Next Greater Element II (Circular Array)

```python
def nextGreaterElements(nums):
    n = len(nums)
    res = [-1] * n
    stack = []

    for i in range(2 * n - 1, -1, -1):
        idx = i % n
        while stack and stack[-1] <= nums[idx]:
            stack.pop()

        if i < n:
            res[idx] = stack[-1] if stack else -1
        stack.append(nums[idx])

    return res
```

---

### 2. Histogram and Rectangle Problems

#### Largest Rectangle in Histogram

```python
def largestRectangleArea(heights):
    stack = []
    max_area = 0
    heights.append(0)

    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)

    return max_area
```

#### Maximal Rectangle in Binary Matrix

```python
def maximalRectangle(matrix):
    if not matrix:
        return 0

    max_area = 0
    heights = [0] * len(matrix[0])

    for row in matrix:
        for i in range(len(row)):
            heights[i] = heights[i] + 1 if row[i] == '1' else 0

        max_area = max(max_area, largestRectangleArea(heights))

    return max_area
```

---

### 3. Stock Span and Range Problems

#### Stock Span Problem

```python
class StockSpanner:
    def __init__(self):
        self.stack = []

    def next(self, price):
        span = 1
        while self.stack and self.stack[-1][0] <= price:
            span += self.stack.pop()[1]

        self.stack.append((price, span))
        return span
```

#### Sum of Subarray Minimums

```python
def sumSubarrayMins(arr):
    MOD = 10**9 + 7
    n = len(arr)
    left = [0] * n
    right = [0] * n

    stack = []
    for i in range(n):
        while stack and arr[stack[-1]] > arr[i]:
            stack.pop()
        left[i] = i - stack[-1] if stack else i + 1
        stack.append(i)

    stack = []
    for i in range(n - 1, -1, -1):
        while stack and arr[stack[-1]] >= arr[i]:
            stack.pop()
        right[i] = stack[-1] - i if stack else n - i
        stack.append(i)

    result = 0
    for i in range(n):
        result = (result + arr[i] * left[i] * right[i]) % MOD

    return result
```

---

### 4. Trapping Rain Water

```python
def trap(height):
    if not height:
        return 0

    water = 0
    stack = []

    for i, h in enumerate(height):
        while stack and height[stack[-1]] < h:
            bottom = stack.pop()
            if not stack:
                break
            distance = i - stack[-1] - 1
            bounded_height = min(height[stack[-1]], h) - height[bottom]
            water += distance * bounded_height

        stack.append(i)

    return water
```

---

### 5. Expression and String Problems

#### Remove K Digits

```python
def removeKdigits(num, k):
    stack = []

    for digit in num:
        while stack and k > 0 and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)

    stack = stack[:-k] if k else stack
    result = ''.join(stack).lstrip('0')
    return result if result else '0'
```

#### Decode String

```python
def decodeString(s):
    stack = []
    current_string = ""
    current_num = 0

    for char in s:
        if char.isdigit():
            current_num = current_num * 10 + int(char)
        elif char == '[':
            stack.append((current_string, current_num))
            current_string = ""
            current_num = 0
        elif char == ']':
            prev_string, num = stack.pop()
            current_string = prev_string + current_string * num
        else:
            current_string += char

    return current_string
```

---

## Quick Reference Table

| Pattern | Direction    | Loop     | Pop Condition | Use Case         |
| ------- | ------------ | -------- | ------------- | ---------------- |
| PSE     | Left → Right | Forward  | `>= x`        | Smaller on left  |
| NSE     | Right → Left | Backward | `>= x`        | Smaller on right |
| PGE     | Left → Right | Forward  | `<= x`        | Greater on left  |
| NGE     | Right → Left | Backward | `<= x`        | Greater on right |

---

## Complexity Analysis

* **Time Complexity:** `O(n)`

  * Each element is pushed once.
  * Each element is popped once.
* **Space Complexity:** `O(n)`

  * Stack and result array.

---

## Implementation Checklist

```python
def monotonic_stack_template(nums):
    stack = []
    res = []

    # Choose direction
    # for x in nums:
    # for i in range(len(nums) - 1, -1, -1):

    # Pop with correct condition
    # while stack and stack[-1] >= x:
    # while stack and stack[-1] <= x:

    # Update result
    # res.append(...)
    # res[i] = ...

    # Push to stack
    # stack.append(x)

    return res
```

---

## Final Takeaway

Monotonic stacks are about maintaining order to discover relationships efficiently.

**Rule to remember:**
Each element goes in once, comes out once, and solves the problem in linear time.
