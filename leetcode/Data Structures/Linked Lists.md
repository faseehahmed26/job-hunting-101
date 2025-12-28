# Linked Lists: An Overview

A linked list is a linear data structure where elements, called nodes, are connected using pointers. Each node consists of:

- **Data:** The value stored in the node  
- **Next Pointer:** A reference to the next node in the list  

---

## Types of Linked Lists

### Singly Linked List (SLL)
- Each node has a data field and a next pointer to the next node  
- Traversal is possible only in one direction  

### Doubly Linked List (DLL)
- Each node has data, a next pointer (to the next node), and a prev pointer (to the previous node)  
- Allows traversal in both directions  

### Circular Linked List (CLL)
- The last node points back to the head node  
- Can be singly or doubly linked  

### Skip List
- A multi-level linked list that allows faster search operations  

---

## Common Operations on Linked Lists

### 1. Traversal
**Objective:** Visit all nodes  

**Approach:**  
- SLL: Traverse using the next pointer  
- DLL: Use next for forward traversal or prev for backward traversal  

---

### 2. Insertion
**Objective:** Add a node at a specific position (start, middle, or end)  

**Approach:**  
Update the pointers of the adjacent nodes  

**Example: Insert at the beginning of an SLL**
```python
def insert_at_head(head, data):
    new_node = Node(data)
    new_node.next = head
    return new_node
````

---

### 3. Deletion

**Objective:** Remove a node based on a condition or position

**Approach:**
Find the node to delete, update the adjacent pointers, and free memory

**Example: Delete a node with a specific value**

```python
def delete_node(head, target):
    if not head:
        return None
    if head.data == target:
        return head.next
    current = head
    while current.next and current.next.data != target:
        current = current.next
    if current.next:
        current.next = current.next.next
    return head
```

---

### 4. Reversal

**Objective:** Reverse the order of nodes

**Approach:**

* Iterative: Use three pointers (prev, current, next) to reverse links
* Recursive: Reverse pointers starting from the head

**Example**

```python
def reverse_list(head):
    prev = None
    current = head
    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    return prev
```

---

### 5. Search

**Objective:** Find a node with a specific value

**Approach:**
Traverse the list and check each node’s data

**Example**

```python
def search_list(head, target):
    while head:
        if head.data == target:
            return True
        head = head.next
    return False
```

---

### 6. Cycle Detection

**Objective:** Check if the linked list has a cycle

**Approach:**
Use Floyd’s Cycle Detection Algorithm (Tortoise and Hare)

**Example**

```python
def has_cycle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

---

### 7. Merge Two Sorted Lists

**Objective:** Combine two sorted linked lists into one sorted list

**Approach:**
Use a dummy node and merge using two pointers

**Example**

```python
def merge_sorted_lists(l1, l2):
    dummy = Node(0)
    current = dummy
    while l1 and l2:
        if l1.data < l2.data:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    current.next = l1 or l2
    return dummy.next
```

---

### 8. Find Middle Node

**Objective:** Find the middle node of the linked list

**Approach:**
Use two pointers (slow and fast). Move slow one step and fast two steps at a time

**Example**

```python
def find_middle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

---

### 9. Remove N-th Node from End

**Objective:** Remove the n-th node from the end of the list

**Approach:**
Use a two-pointer technique, keeping a gap of n nodes

**Example**

```python
def remove_nth_from_end(head, n):
    dummy = Node(0)
    dummy.next = head
    first = second = dummy
    for _ in range(n + 1):
        first = first.next
    while first:
        first = first.next
        second = second.next
    second.next = second.next.next
    return dummy.next
```

---

### 10. Flattening a Multilevel Linked List

**Objective:** Flatten a linked list where nodes can have child pointers to another list

**Approach:**
Use a stack or recursion to process child nodes

---

## Techniques for Solving Linked List Problems

### 1. Two Pointer Technique

Useful for detecting cycles, finding middle nodes, or checking intersections

### 2. Dummy Node

Simplifies edge cases, such as inserting or deleting at the head

### 3. Recursion

Useful for reversing linked lists or flattening nested lists

### 4. Divide and Conquer

Example: Merge K sorted linked lists using pairwise merging or a priority queue

### 5. Sliding Window

Example: Finding a sublist that satisfies certain conditions

---

## Tips for Solving Linked List Problems

* **Understand Node Structure:**
  Be clear about how nodes are connected and updated

* **Visualize with Diagrams:**
  Drawing diagrams helps track pointer changes

* **Handle Edge Cases:**
  Empty lists, single-node lists, and cyclic lists

* **Time Complexity:**
  Most operations run in `O(n)` time

* **Memory Management:**
  In languages like C or C++, free unused memory to avoid leaks

* **Practice Common Patterns:**
  Reversal, merging, and cycle detection are core linked list patterns

---

By mastering these concepts and techniques, you can confidently solve a wide range of linked list problems.

```

