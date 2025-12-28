````md
https://leetcode.com/tag/math/

https://leetcode.com/tag/geometry/

https://www.geeksforgeeks.org/computational-geometry/

# Math & Geometry: Complete Interview Guide

Math and Geometry problems require **formal mathematical reasoning**, **spatial intuition**, and **precise implementation**. These problems are common in interviews because they test correctness, edge-case handling, and numerical robustness.

---

## 1. Identifying a Math & Geometry Problem

### Key Signals in the Problem Statement
Look for keywords or concepts involving:
- Shapes, points, lines, circles, polygons
- Coordinates (2D or 3D)
- Distances, angles, slopes
- Area, perimeter, volume
- Intersections or overlaps
- Modular arithmetic or numeric patterns
- Optimization with spatial constraints

If the problem requires **formulas**, **coordinate manipulation**, or **spatial reasoning**, it is almost certainly a Math & Geometry problem.

---

## 2. Critical Clarifying Questions

### Input Constraints
- Are coordinates integers or floating-point values?
- Can inputs be negative?
- Are degenerate cases possible?
  - Zero-length lines
  - Collinear points
  - Overlapping shapes
- What are the bounds of the inputs?

### Precision and Output
- Is floating-point precision important?
- Should results be rounded?
- How many decimal places are required?
- Is exact equality expected or approximate comparison?

### Performance
- How large is the input?
- Is an O(n) or O(n log n) solution required?
- Are repeated geometric queries expected?

### Ambiguities to Resolve
- How to handle coincident points?
- How to handle overlapping or touching shapes?
- Are irrational values like π or square roots acceptable directly?

---

## 3. Problem-Solving Approach

### a. Data Representation

#### Points
```python
(x, y)        # 2D
(x, y, z)     # 3D
````

#### Shapes

* Line: two points
* Rectangle: corner points or (x, y, width, height)
* Circle: center + radius
* Polygon: list of points

---

### b. Core Mathematical Tools

#### Distance Between Two Points

```math
\sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}
```

#### Area of Triangle (Shoelace Formula)

```math
0.5 × |x₁(y₂ − y₃) + x₂(y₃ − y₁) + x₃(y₁ − y₂)|
```

#### Slope of Line

```math
(y₂ − y₁) / (x₂ − x₁)
```

---

### c. Algorithm Design Steps

1. Translate the problem into math formulas
2. Write helper functions for reusable calculations
3. Iterate through points or shapes as required
4. Apply pruning or symmetry when possible
5. Optimize by sorting or geometric properties

---

### d. Optimization Opportunities

* Cache repeated distance or slope calculations
* Use squared distances to avoid square roots when possible
* Reduce precision errors using math or decimal libraries
* Exploit symmetry in shapes or configurations

---

## 4. Time and Space Complexity

### Time Complexity

* Single distance or area computation: O(1)
* Sorting points: O(n log n)
* Pairwise comparisons: O(n²)

### Space Complexity

* Constant extra space: O(1)
* Storing points or results: O(n)

---

## 5. Example Problems

### Example 1: Distance Between Two Points

#### Formula

```math
d = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}
```

#### Python Implementation

```python
import math

def distance_between_points(p1, p2):
    x1, y1 = p1
    x2, y2 = p2
    return math.sqrt((x2 - x1)**2 + (y2 - y1)**2)
```

#### Test Cases

```python
assert distance_between_points((0, 0), (3, 4)) == 5.0
assert distance_between_points((1, 1), (1, 1)) == 0.0
assert round(distance_between_points((-2, -3), (4, 5)), 2) == 10.0
```

---

### Example 2: Area of a Triangle

#### Formula (Shoelace)

```math
0.5 × |x₁(y₂ − y₃) + x₂(y₃ − y₁) + x₃(y₁ − y₂)|
```

#### Python Implementation

```python
def triangle_area(p1, p2, p3):
    x1, y1 = p1
    x2, y2 = p2
    x3, y3 = p3
    return 0.5 * abs(
        x1*(y2-y3) + x2*(y3-y1) + x3*(y1-y2)
    )
```

#### Test Cases

```python
assert triangle_area((0, 0), (4, 0), (0, 3)) == 6.0
assert triangle_area((0, 0), (1, 1), (2, 2)) == 0.0
assert round(triangle_area((1, 2), (4, 6), (5, 3)), 2) == 5.5
```

---

## 6. Common Pitfalls

* Floating-point precision errors
* Forgetting degenerate cases
* Assuming valid geometry without checking
* Overcomplicating formulas
* Incorrect rounding or comparison logic

---

## 7. Live Coding Tips

* Explain the math before writing code
* Derive formulas verbally
* Use helper functions for clarity
* Validate edge cases explicitly
* Test with simple numeric examples

---

## Interview Recognition Checklist

If the problem involves:

* Coordinates or shapes
* Distance or area calculations
* Spatial constraints
* Numeric formulas

Then immediately think **Math & Geometry**, not brute force.

---

**Key Takeaway**
Math and Geometry problems reward correctness and clarity. If you model the problem mathematically first, the code becomes straightforward and robust.

```
```
