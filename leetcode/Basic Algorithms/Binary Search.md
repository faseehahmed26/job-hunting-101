# Binary Search Notes (Interview Quick Guide)

Binary search reduces search from **O(n)** to **O(log n)** by repeatedly halving a **search space**. The key requirement is **monotonicity**: once a condition becomes true (or false), it stays that way on one side.

## When to use
- **First or last occurrence**, **lower bound**, **upper bound**
- **Minimum** or **maximum** value that satisfies a constraint
- **Kth** smallest or largest using a counting or feasibility check
- “Optimize under constraints” (capacity, time, speed, distance)

## Two main templates

### 1) Exact search in sorted array (find target)
Use when you want the index of a value (or return -1 if missing).
```python
l, r = 0, len(nums) - 1
while l <= r:
    mid = l + (r - l) // 2
    if nums[mid] == target:
        return mid
    if nums[mid] < target:
        l = mid + 1
    else:
        r = mid - 1
return -1
````

### 2) Boundary search (first True, lower bound)

Use when you can write a monotonic `condition(x)`.

```python
def binary_search(left, right):
    def condition(x) -> bool:
        pass

    while left < right:
        mid = left + (right - left) // 2
        if condition(mid):
            right = mid
        else:
            left = mid + 1
    return left
```

**Rules to remember**

* Choose `[left, right]` (or `[left, right)`) so it includes all possible answers.
* If `condition(mid)` is True and you want the first True, move `right = mid`.
* Return value is typically the **smallest valid** answer (lower bound).

## Common patterns

### Lower bound and upper bound

* **First index with `nums[i] >= target`**: lower bound
* **First index with `nums[i] > target`**: upper bound

### Peak finding

Compare neighbors and move toward the side that must contain a peak.

### Rotated array

Identify which half is sorted, then decide which half can contain the target.

### Binary search on answer (feasibility)

Search an integer answer range; each mid is checked by a `can(mid)` function.
Examples: Koko eating bananas, ship packages within D days, split array largest sum.

### Kth element via counting

Search a value `x` and count how many elements are `<= x`. Use monotonic count.

## Variations

### Find maximum valid (last True)

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

### Floating point binary search

Stop when interval is smaller than `eps`.

## Complexity

* **Time:** `O(log N)` iterations
* If each check costs `O(C)`, total is `O(C log N)`
* **Space:** `O(1)` for iterative binary search

## Common mistakes

* Off-by-one in boundaries (`right = len(nums)` vs `len(nums)-1`)
* Wrong loop condition (`l < r` vs `l <= r`)
* Wrong mid for “last true” (need upper mid)
* Condition is not monotonic

## Practice set

* 704, 35, 69
* 162, 33, 875
* 410, 668, 4

## References

* LeetCode Discuss: Binary Search Comprehensive Guide
  [https://leetcode.com/discuss/post/3726061/binary-search-a-comprehensive-guide-by-i-3nxx/](https://leetcode.com/discuss/post/3726061/binary-search-a-comprehensive-guide-by-i-3nxx/)
* LeetCode Discuss: Python Ultimate Binary Search Template
  [https://leetcode.com/discuss/post/786126/python-powerful-ultimate-binary-search-t-rwv8/](https://leetcode.com/discuss/post/786126/python-powerful-ultimate-binary-search-t-rwv8/)

```
```
