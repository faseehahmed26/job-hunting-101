````md
https://leetcode.com/tag/intervals/

https://leetcode.com/problems/merge-intervals/

https://leetcode.com/problems/meeting-rooms-ii/

https://leetcode.com/problems/interval-list-intersections/

# Interval Approach: Complete Interview Guide

The **Interval pattern** is used for problems involving **ranges**, **time windows**, or **start–end boundaries**. These problems almost always rely on **sorting + greedy traversal**.

---

## 1. Pattern Identification  
### How to Recognize an Interval Problem

### Key Triggers in the Problem Statement
- Input is a list of **intervals / ranges**
- Each element has a **start and end**
- You are asked to:
  - Merge intervals
  - Detect overlaps
  - Find gaps
  - Allocate resources
  - Schedule meetings

### Common Trigger Words
- “intervals”
- “overlapping”
- “merge”
- “schedule”
- “meeting rooms”
- “booking”
- “availability”
- “timeline”

If the input looks like:
```python
[[start1, end1], [start2, end2], ...]
````

you should immediately think **Interval Pattern**.

---

## 2. Core Problem-Solving Framework

### Step 1: Initial Analysis

Ask yourself:

* Are intervals sorted?
* Are they inclusive or exclusive?
* Can intervals overlap?
* Do I need to merge, count, or select intervals?

### Step 2: High-Level Strategy

Most interval problems follow this structure:

1. **Sort intervals** (usually by start time)
2. **Traverse sequentially**
3. **Compare current interval with previous**
4. Decide:

   * Merge
   * Skip
   * Count
   * Allocate resource

### Step 3: Choose Data Structure

* **Array + traversal** → merging, intersection
* **Min-heap** → meeting rooms, resource allocation

---

## 3. Essential Interval Patterns (with Examples)

---

### Pattern 1: Merge Overlapping Intervals

**When to Use**

* Combine overlapping time ranges
* Compress ranges
* Union of intervals

```python
def merge_intervals(intervals):
    if not intervals:
        return []

    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]

    for start, end in intervals[1:]:
        last_end = merged[-1][1]
        if start <= last_end:
            merged[-1][1] = max(last_end, end)
        else:
            merged.append([start, end])

    return merged
```

**Time Complexity**: O(n log n)
**Space Complexity**: O(n)

---

### Pattern 2: Meeting Rooms / Resource Allocation

**When to Use**

* Minimum number of rooms
* Scheduling conflicts
* Parallel resources

```python
import heapq

def min_meeting_rooms(intervals):
    if not intervals:
        return 0

    intervals.sort(key=lambda x: x[0])
    heap = []

    for start, end in intervals:
        if heap and heap[0] <= start:
            heapq.heappop(heap)
        heapq.heappush(heap, end)

    return len(heap)
```

**Key Insight**
Heap always tracks **earliest ending meeting**.

**Time Complexity**: O(n log n)

---

### Pattern 3: Interval Intersection

**When to Use**

* Find common availability
* Overlap between two schedules

```python
def interval_intersection(A, B):
    result = []
    i = j = 0

    while i < len(A) and j < len(B):
        start = max(A[i][0], B[j][0])
        end = min(A[i][1], B[j][1])

        if start <= end:
            result.append([start, end])

        if A[i][1] < B[j][1]:
            i += 1
        else:
            j += 1

    return result
```

**Time Complexity**: O(n + m)

---

### Pattern 4: Insert Interval

**When to Use**

* Add a new booking
* Modify schedule dynamically

```python
def insert_interval(intervals, new_interval):
    result = []

    for i, (start, end) in enumerate(intervals):
        if new_interval[1] < start:
            result.append(new_interval)
            return result + intervals[i:]
        elif new_interval[0] > end:
            result.append([start, end])
        else:
            new_interval[0] = min(new_interval[0], start)
            new_interval[1] = max(new_interval[1], end)

    result.append(new_interval)
    return result
```

---

## 4. Universal Interval Template

```python
def interval_problem(intervals):
    if not intervals:
        return []

    intervals.sort(key=lambda x: x[0])
    result = []

    for interval in intervals:
        if not result or interval[0] > result[-1][1]:
            result.append(interval)
        else:
            result[-1][1] = max(result[-1][1], interval[1])

    return result
```

---

## 5. Problem-Solving Checklist

### Before Coding

* [ ] Do intervals need sorting?
* [ ] Are overlaps allowed?
* [ ] What defines an overlap?
* [ ] What should happen on overlap?

### While Coding

* [ ] Handle empty input
* [ ] Handle single interval
* [ ] Compare current start with previous end
* [ ] Update boundaries carefully

---

## 6. Common Strategies

### Strategy 1: Sorting + Greedy

Used for:

* Merge intervals
* Insert interval
* Remove overlaps

### Strategy 2: Two Pointers

Used for:

* Interval intersection
* Comparing two schedules

### Strategy 3: Min-Heap

Used for:

* Meeting rooms
* Resource allocation
* Dynamic interval tracking

---

## 7. Essential Edge Cases

* Empty input
* Single interval
* Fully overlapping intervals
* Fully disjoint intervals
* Negative values
* Invalid intervals (start > end)

---

## 8. Pattern Relationships

### Related Patterns

* Greedy
* Two pointers
* Heap

### Key Difference

* Sliding Window → continuous subarray
* Interval → discrete ranges

---

## 9. Real Interview Examples

### Merge Intervals

Why:

* Sorting + greedy traversal
* Overlap detection

### Meeting Rooms

Why:

* Overlap defines resource count
* Heap tracks earliest finish

---

## 10. Quick Reference

### Time & Space Complexity

* Sorting: O(n log n)
* Traversal: O(n)
* Heap operations: O(log n)

### Must-Remember Rules

* Always sort unless guaranteed sorted
* Compare current start with previous end
* Use heap when resources are reused dynamically

---

## 11. Interview Execution Template

1. Identify interval pattern
2. Sort intervals
3. Choose:

   * Two pointers OR
   * Heap
4. Implement merge / count / allocate logic
5. Test edge cases
6. Explain complexity

---

**Final Insight**
If the problem involves **ranges over time or space**, your first instinct should be:

> “Sort the intervals and traverse greedily.”

Master this pattern and you will unlock an entire class of interview problems.

```
```
