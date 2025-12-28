```md
# Tries

## Tries: An Overview

A **Trie** (pronounced "try") is a tree-like data structure used for storing strings efficiently. It is especially useful for prefix-based searching, such as autocomplete, dictionary lookup, IP routing, and spell checking.

---

## Structure of a Trie

A Trie consists of:

- **Nodes:** Each node represents a character.
- **Edges:** Connect nodes to form words.
- **Root Node:** Represents an empty string `""`.
- **End Marker:** Some nodes mark the end of a valid word.

### Example  
Storing the words: `cat`, `cap`, `can`, `bat`, `bad`

```

```
    (root)
    /    \
   c      b
   |      |
   a      a
 / | \    | \
t  p  n   t  d
```

````

---

## Trie Operations and Time Complexity

| Operation       | Time Complexity |
|-----------------|-----------------|
| Insert a word   | O(N) |
| Search a word   | O(N) |
| Delete a word   | O(N) |
| Prefix search   | O(N) |

Where `N` is the length of the word or prefix.

---

## Trie Implementation in Python

### 1. Insert and Search

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end_of_word = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end_of_word
````

#### Example Usage

```python
trie = Trie()
trie.insert("cat")
trie.insert("cap")

print(trie.search("cat"))  # True
print(trie.search("can"))  # False
```

---

### 2. Prefix Search (Autocomplete)

```python
class TrieWithPrefix(Trie):
    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

#### Example

```python
trie = TrieWithPrefix()
trie.insert("hello")

print(trie.starts_with("hel"))  # True
print(trie.starts_with("hey"))  # False
```

---

### 3. Deleting a Word from a Trie

```python
class TrieWithDelete(Trie):
    def delete(self, word):
        def helper(node, word, depth):
            if depth == len(word):
                if not node.is_end_of_word:
                    return False
                node.is_end_of_word = False
                return len(node.children) == 0

            char = word[depth]
            if char not in node.children:
                return False

            should_delete = helper(node.children[char], word, depth + 1)

            if should_delete:
                del node.children[char]
                return len(node.children) == 0

            return False

        helper(self.root, word, 0)
```

#### Example

```python
trie = TrieWithDelete()
trie.insert("bat")
trie.delete("bat")

print(trie.search("bat"))  # False
```

---

## Applications of Tries

* **Autocomplete and Search Suggestions**
  Example: searching `"app"` returns `["apple", "application"]`.

* **Spell Checking**
  Used in word processors to detect invalid words.

* **IP Routing (Radix Tries)**
  Used in networking for routing table lookups.

* **Dictionary and Word Games**
  Used in Scrabble solvers and word search problems.

* **Predictive Text (T9)**
  Efficiently predicts words from numeric input.

---

## Trie vs Other Data Structures

| Feature          | Trie            | Hash Table   | Binary Search Tree |
| ---------------- | --------------- | ------------ | ------------------ |
| Lookup Time      | O(N)            | O(1) average | O(log N)           |
| Memory Usage     | High            | Low          | Medium             |
| Prefix Search    | O(N), efficient | O(N), slow   | O(log N)           |
| Space Complexity | O(alphabet × N) | O(N)         | O(N)               |

* Tries outperform hash tables for prefix searches.
* Tries consume more memory due to character-level storage.

---

## Problem Solving with Tries

### Choosing the Right Representation

* Dictionary-based Trie for dynamic alphabets.
* Fixed-size array of size 26 for lowercase English letters.

### Memory Optimization

* Use compressed tries such as Radix Tries.
* Use Ternary Search Trees for memory efficiency.

---

## Tips for Trie-Based Problems

* Visualize the Trie structure before coding.
* Use recursion for deletion and autocomplete DFS.
* Separate full-word search from prefix search.
* Be mindful of memory usage for large datasets.

---

## Test Cases

### Happy Path

```python
trie = Trie()
trie.insert("apple")

print(trie.search("apple"))       # True
print(trie.starts_with("app"))    # True
print(trie.search("app"))         # False
```

### Edge Cases

**Empty Trie**

```python
print(trie.search(""))        # False
print(trie.starts_with(""))   # True
```

**Single Character**

```python
trie.insert("a")
print(trie.search("a"))       # True
print(trie.starts_with("a"))  # True
```

**Complex Case**

```python
trie.insert("cat")
trie.insert("car")
trie.insert("cart")

print(trie.search("car"))     # True
print(trie.starts_with("ca")) # True
print(trie.search("cap"))     # False
```

---

## Performance Analysis

### Time Complexity

* Insert, search, prefix match: `O(k)` where `k` is word length.

### Space Complexity

* Worst case: `O(n × k)` where `n` is number of words.

---

## Common Pitfalls

* Forgetting to handle empty strings.
* Confusing `search` with `starts_with`.
* Missing child node initialization during insertion.
* Incorrect deletion logic causing prefix loss.

---

## Live Coding Tips

* Clearly explain Trie structure and operations.
* Write modular code with helper functions.
* Clarify constraints such as case sensitivity.
* Test edge cases during the interview.

---

## Extensions

* Implement word deletion.
* Add autocomplete that returns all matching words using DFS.

---

By mastering Tries, you will be well prepared to solve autocomplete, dictionary search, and prefix-matching problems efficiently.

```
```
