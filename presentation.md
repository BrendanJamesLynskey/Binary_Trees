# Binary Trees

**Computer Science Fundamentals Series**

Traversals | Tree properties | LCA | Morris traversal | Expression trees | Serialisation

*Mid-level software engineer track -- 20 slides*

---

## Table of Contents

1. [Tree Terminology](#slide-02--tree-terminology)
2. [Binary Tree Definition & Properties](#slide-03--binary-tree-definition--properties)
3. [Full & Complete Binary Trees](#slide-04--full--complete-binary-trees)
4. [Perfect, Balanced & Degenerate Trees](#slide-05--perfect-balanced--degenerate-trees)
5. [Inorder Traversal](#slide-06--inorder-traversal)
6. [Preorder & Postorder Traversal](#slide-07--preorder--postorder-traversal)
7. [Iterative Traversals](#slide-08--iterative-traversals)
8. [Level-Order Traversal (BFS)](#slide-09--level-order-traversal-bfs)
9. [Morris Traversal](#slide-10--morris-traversal)
10. [Constructing Trees from Traversals](#slide-11--constructing-trees-from-traversals)
11. [Lowest Common Ancestor](#slide-12--lowest-common-ancestor)
12. [Diameter of a Binary Tree](#slide-13--diameter-of-a-binary-tree)
13. [Serialisation & Deserialisation](#slide-14--serialisation--deserialisation)
14. [Threaded Binary Trees](#slide-15--threaded-binary-trees)
15. [Expression Trees](#slide-16--expression-trees)
16. [Applications -- File Systems](#slide-17--applications--file-systems)
17. [Applications -- DOM & Decision Trees](#slide-18--applications--dom--decision-trees)
18. [Complexity Cheat Sheet](#slide-19--complexity-cheat-sheet)
19. [Summary & Further Reading](#slide-20--summary--further-reading)

---

## Slide 02 -- Tree Terminology

### Fundamental vocabulary

Every tree conversation starts with shared terminology. A tree is a connected, acyclic graph with a distinguished root node.

| Term | Definition |
|------|-----------|
| **Root** | The topmost node -- has no parent |
| **Parent** | A node directly above another in the hierarchy |
| **Child** | A node directly below another -- each node may have zero or more children |
| **Leaf** | A node with no children -- also called an external node |
| **Internal node** | A node with at least one child |
| **Edge** | The link between a parent and a child |
| **Depth** | Number of edges from the root to a given node -- root has depth 0 |
| **Height** | Number of edges on the longest path from a node down to a leaf -- leaf has height 0 |
| **Level** | Set of all nodes at the same depth |
| **Subtree** | A node and all its descendants -- every node is the root of its own subtree |

> A tree with `n` nodes always has exactly `n - 1` edges.

---

## Slide 03 -- Binary Tree Definition & Properties

### Definition

A binary tree is a tree in which every node has at most two children, conventionally labelled **left** and **right**.

### Key properties

- Maximum nodes at depth `d`: `2^d`
- Maximum total nodes with height `h`: `2^(h+1) - 1`
- Minimum height for `n` nodes: `floor(log2(n))`
- Number of NULL pointers in a binary tree with `n` nodes: `n + 1`
- A binary tree with `n` internal nodes has `n + 1` leaves (for full binary trees)

### Node structure

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val   = val
        self.left  = left
        self.right = right
```

> The recursive structure of `TreeNode` mirrors the recursive nature of every binary tree algorithm.

---

## Slide 04 -- Full & Complete Binary Trees

### Full binary tree

Every node has either **0 or 2** children -- no node has exactly one child.

```
        1
       / \
      2   3
     / \
    4   5
```

- With `i` internal nodes: exactly `i + 1` leaves, `2i + 1` total nodes
- With `L` leaves: `2L - 1` total nodes

### Complete binary tree

All levels are completely filled except possibly the last, which is filled from left to right.

```
        1
       / \
      2   3
     / \ /
    4  5 6
```

- Height is always `floor(log2(n))` -- guarantees `O(log n)` operations
- Can be stored efficiently in an array: node at index `i` has children at `2i + 1` and `2i + 2`
- Binary heaps are complete binary trees

> Complete trees give the most compact shape for a given number of nodes.

---

## Slide 05 -- Perfect, Balanced & Degenerate Trees

### Perfect binary tree

Every internal node has exactly 2 children and all leaves are at the same depth.

- Nodes at each level: `2^d`. Total: `2^(h+1) - 1`
- A perfect binary tree of height `h` has `2^h` leaves
- Both full and complete -- the strictest shape

### Balanced binary tree

The height difference between left and right subtrees of every node is at most 1.

- Guarantees `O(log n)` height
- AVL trees, red-black trees, and B-trees maintain balance through rotations or splits
- An unbalanced tree can degrade to `O(n)` height

### Degenerate (pathological) tree

Every internal node has exactly one child -- effectively a linked list.

```
1 -> 2 -> 3 -> 4 -> 5
```

- Height = `n - 1` -- worst case for all tree operations
- Search, insert, delete all become `O(n)`
- This is why self-balancing trees exist

---

## Slide 06 -- Inorder Traversal

### Left -> Root -> Right

Visit the left subtree, then the current node, then the right subtree. For a BST, inorder traversal produces sorted output.

```python
def inorder(root):
    if root is None:
        return
    inorder(root.left)
    visit(root.val)
    inorder(root.right)
```

### Example

```
        4
       / \
      2   6
     / \ / \
    1  3 5  7

Inorder: 1, 2, 3, 4, 5, 6, 7  (sorted!)
```

### Applications

- Retrieve BST elements in sorted order
- Expression tree evaluation (infix notation)
- Validate whether a binary tree is a BST

> Time: `O(n)`. Space: `O(h)` for the recursion stack, where `h` is the tree height.

---

## Slide 07 -- Preorder & Postorder Traversal

### Preorder: Root -> Left -> Right

```python
def preorder(root):
    if root is None:
        return
    visit(root.val)
    preorder(root.left)
    preorder(root.right)
```

- First element is always the root
- Used to serialise a tree -- preorder sequence + structure info can reconstruct the tree
- Creates a copy of the tree

### Postorder: Left -> Right -> Root

```python
def postorder(root):
    if root is None:
        return
    postorder(root.left)
    postorder(root.right)
    visit(root.val)
```

- Last element is always the root
- Used to delete a tree (children before parent)
- Expression tree evaluation (postfix / reverse Polish notation)
- Calculating directory sizes (compute children before summing parent)

### Example on the same tree

```
Preorder:  4, 2, 1, 3, 6, 5, 7
Postorder: 1, 3, 2, 5, 7, 6, 4
```

---

## Slide 08 -- Iterative Traversals

### Why iterative?

Recursion uses `O(h)` stack space implicitly. For very deep trees, this risks stack overflow. Iterative versions use an explicit stack.

### Iterative inorder

```python
def inorder_iterative(root):
    stack, current = [], root
    while stack or current:
        while current:
            stack.append(current)
            current = current.left
        current = stack.pop()
        visit(current.val)
        current = current.right
```

### Iterative preorder

```python
def preorder_iterative(root):
    if not root:
        return
    stack = [root]
    while stack:
        node = stack.pop()
        visit(node.val)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
```

> Postorder iterative is trickiest -- use two stacks or reverse a modified preorder (Root -> Right -> Left).

---

## Slide 09 -- Level-Order Traversal (BFS)

### Breadth-first, level by level

Visit all nodes at depth `d` before moving to depth `d + 1`. Uses a queue instead of a stack.

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    result, queue = [], deque([root])
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

### Example

```
        4
       / \
      2   6
     / \ / \
    1  3 5  7

Level-order: [[4], [2, 6], [1, 3, 5, 7]]
```

### Applications

- Finding minimum depth of a tree
- Zigzag traversal (alternate left-to-right, right-to-left per level)
- Connecting nodes at the same level (next pointers)
- Serialisation (level by level with NULL markers)

> Time: `O(n)`. Space: `O(w)` where `w` is the maximum width of the tree (up to `n/2` at the last level).

---

## Slide 10 -- Morris Traversal

### O(1) space, no stack, no recursion

Morris traversal achieves `O(n)` time with `O(1)` extra space by temporarily modifying the tree using **threaded pointers**.

### Algorithm (inorder)

```python
def morris_inorder(root):
    current = root
    while current:
        if current.left is None:
            visit(current.val)
            current = current.right
        else:
            # Find inorder predecessor
            pred = current.left
            while pred.right and pred.right != current:
                pred = pred.right

            if pred.right is None:
                # Thread: link predecessor to current
                pred.right = current
                current = current.left
            else:
                # Unthread: restore the tree
                pred.right = None
                visit(current.val)
                current = current.right
```

### How it works

1. If no left child -- visit node, move right
2. If left child exists -- find the **inorder predecessor** (rightmost node in left subtree)
3. If predecessor's right is NULL -- create a temporary thread back to current, go left
4. If predecessor's right points to current -- thread already exists, remove it, visit current, go right

> The tree is restored to its original shape. Each edge is traversed at most twice, maintaining `O(n)` time.

---

## Slide 11 -- Constructing Trees from Traversals

### Which pairs uniquely determine a tree?

| Pair | Unique tree? |
|------|-------------|
| **Inorder + Preorder** | Yes |
| **Inorder + Postorder** | Yes |
| **Inorder + Level-order** | Yes |
| **Preorder + Postorder** | Only for full binary trees |

> Inorder is essential -- it tells you which nodes belong to the left vs right subtree.

### Algorithm: inorder + preorder

```python
def build_tree(preorder, inorder):
    if not inorder:
        return None
    root_val = preorder.pop(0)
    root = TreeNode(root_val)
    mid = inorder.index(root_val)
    root.left  = build_tree(preorder, inorder[:mid])
    root.right = build_tree(preorder, inorder[mid+1:])
    return root
```

### Optimisation

Use a hash map for `O(1)` index lookup instead of `inorder.index()`. Use a pointer into preorder instead of `pop(0)`. This brings the total time from `O(n^2)` to `O(n)`.

---

## Slide 12 -- Lowest Common Ancestor

### Definition

The lowest common ancestor (LCA) of two nodes `p` and `q` is the deepest node that is an ancestor of both.

### Recursive approach

```python
def lca(root, p, q):
    if root is None or root == p or root == q:
        return root
    left  = lca(root.left, p, q)
    right = lca(root.right, p, q)
    if left and right:
        return root      # p and q are in different subtrees
    return left or right  # both in the same subtree
```

### How it works

- If the current node is `p` or `q`, return it
- Recurse left and right
- If both sides return non-NULL, current node is the LCA
- If only one side returns non-NULL, the LCA is in that subtree

### Complexity

- Time: `O(n)` -- visit every node in the worst case
- Space: `O(h)` -- recursion stack

> For a BST, you can use the BST property: if both `p` and `q` are smaller than root, go left; if both are larger, go right; otherwise root is the LCA. This gives `O(h)` time.

---

## Slide 13 -- Diameter of a Binary Tree

### Definition

The diameter (or width) of a binary tree is the **longest path between any two nodes**, measured in number of edges. The path may or may not pass through the root.

### Algorithm

```python
def diameter(root):
    max_diameter = 0

    def height(node):
        nonlocal max_diameter
        if node is None:
            return -1
        left_h  = height(node.left)
        right_h = height(node.right)
        # Path through this node
        max_diameter = max(max_diameter,
                          left_h + right_h + 2)
        return max(left_h, right_h) + 1

    height(root)
    return max_diameter
```

### Key insight

At each node, the longest path through that node is `left_height + right_height + 2`. The diameter is the maximum across all nodes. We compute this in a single `O(n)` DFS pass by combining diameter tracking with height calculation.

> The same pattern applies to many tree problems: compute a local answer at each node while returning information upward. This "post-order accumulation" pattern is fundamental.

---

## Slide 14 -- Serialisation & Deserialisation

### Problem

Convert a binary tree to a string (or byte sequence) that can be stored or transmitted, and reconstruct the original tree from that string.

### Preorder with NULL markers

```python
def serialise(root):
    if root is None:
        return "# "
    return (str(root.val) + " "
            + serialise(root.left)
            + serialise(root.right))

def deserialise(data):
    tokens = iter(data.split())

    def build():
        val = next(tokens)
        if val == "#":
            return None
        node = TreeNode(int(val))
        node.left  = build()
        node.right = build()
        return node

    return build()
```

### Example

```
    1
   / \
  2   3
     / \
    4   5

Serialised: "1 2 # # 3 4 # # 5 # #"
```

### Alternative approaches

- **Level-order** with NULL markers -- natural for BFS, used by LeetCode
- **Inorder + preorder** pair -- no NULLs needed but requires two sequences
- **Parenthesised** format: `1(2)(3(4)(5))` -- human readable

> Serialisation is `O(n)` time and space regardless of approach.

---

## Slide 15 -- Threaded Binary Trees

### The NULL pointer problem

In a binary tree with `n` nodes, there are `n + 1` NULL pointers (wasted space). Threaded binary trees repurpose these NULLs to point to inorder predecessors/successors.

### Types of threading

| Type | Description |
|------|-----------|
| **Single-threaded** | Right NULL pointers point to the inorder successor |
| **Double-threaded** | Left NULLs point to inorder predecessor, right NULLs point to inorder successor |

### Node structure

```python
class ThreadedNode:
    def __init__(self, val):
        self.val = val
        self.left  = None
        self.right = None
        self.left_thread  = False  # True if left is a thread
        self.right_thread = False  # True if right is a thread
```

### Benefits

- Inorder traversal without a stack or recursion -- `O(1)` space
- Finding inorder successor/predecessor in `O(1)` amortised time
- Morris traversal is essentially creating temporary threads on the fly

> Threaded trees trade insertion/deletion complexity for traversal efficiency. Rarely used in practice but important conceptually -- they inspired Morris traversal.

---

## Slide 16 -- Expression Trees

### Definition

An expression tree is a binary tree that represents an arithmetic expression. Leaves are operands, internal nodes are operators.

### Example: `(3 + 4) * (5 - 2)`

```
        *
       / \
      +   -
     / \ / \
    3  4 5  2
```

### Traversals produce different notations

| Traversal | Notation | Output |
|-----------|----------|--------|
| **Inorder** | Infix | `3 + 4 * 5 - 2` (needs parentheses) |
| **Preorder** | Prefix (Polish) | `* + 3 4 - 5 2` |
| **Postorder** | Postfix (RPN) | `3 4 + 5 2 - *` |

### Building from postfix

```python
def build_expression_tree(postfix):
    stack = []
    operators = {'+', '-', '*', '/'}
    for token in postfix:
        node = TreeNode(token)
        if token in operators:
            node.right = stack.pop()
            node.left  = stack.pop()
        stack.append(node)
    return stack[0]
```

### Evaluation

```python
def evaluate(node):
    if node.left is None:    # leaf = operand
        return float(node.val)
    left  = evaluate(node.left)
    right = evaluate(node.right)
    return {'+': lambda a,b: a+b,
            '-': lambda a,b: a-b,
            '*': lambda a,b: a*b,
            '/': lambda a,b: a/b}[node.val](left, right)
```

---

## Slide 17 -- Applications -- File Systems

### Directory trees

File systems are naturally tree-structured. Each directory is an internal node; files are leaves.

```
/
├── home/
│   ├── alice/
│   │   ├── docs/
│   │   │   └── report.pdf
│   │   └── .bashrc
│   └── bob/
│       └── code/
│           └── main.py
├── etc/
│   └── config.yaml
└── var/
    └── log/
        └── syslog
```

### Tree operations in file systems

| Operation | Tree algorithm |
|-----------|---------------|
| `ls -R` (recursive listing) | Preorder DFS |
| `du -s` (directory size) | Postorder DFS -- compute children before parent |
| `find` (search by name) | BFS or DFS depending on implementation |
| `rm -rf` | Postorder -- delete children before parent |
| Path resolution (`/home/alice/docs`) | Root-to-leaf traversal |

> Every `cd`, `ls`, and `mkdir` is a tree operation. Understanding tree traversals helps you reason about file system performance.

---

## Slide 18 -- Applications -- DOM & Decision Trees

### The DOM (Document Object Model)

Every web page is a tree. The browser parses HTML into a tree of nodes.

```
document
└── html
    ├── head
    │   ├── title
    │   └── link
    └── body
        ├── div#header
        │   └── h1
        └── div#content
            ├── p
            └── ul
                ├── li
                └── li
```

- CSS selectors traverse the tree (descendant, child, sibling combinators)
- `document.getElementById()` is a tree search
- DOM diffing (React, Vue) compares two trees to compute minimal updates

### Decision trees

Binary trees where each internal node is a yes/no question and each leaf is a classification.

```
              Is temp > 30?
             /             \
          Yes               No
      Is humid?         Is windy?
       /    \            /     \
     Yes    No        Yes      No
    Stay   Beach     Stay    Park
  inside             inside
```

- Each root-to-leaf path is a decision rule
- Training: recursively split data to maximise information gain
- Random forests: ensemble of many decision trees
- Used in medicine, finance, recommendation systems

---

## Slide 19 -- Complexity Cheat Sheet

### Time and space for common operations

| Operation | Average | Worst (unbalanced) | Balanced |
|-----------|---------|-------------------|----------|
| Search | `O(log n)` | `O(n)` | `O(log n)` |
| Insert | `O(log n)` | `O(n)` | `O(log n)` |
| Delete | `O(log n)` | `O(n)` | `O(log n)` |
| Traversal (any order) | `O(n)` | `O(n)` | `O(n)` |
| Find min/max | `O(log n)` | `O(n)` | `O(log n)` |
| LCA | `O(n)` | `O(n)` | `O(log n)` with parent ptrs |

### Space complexity of traversals

| Method | Space |
|--------|-------|
| Recursive DFS | `O(h)` -- recursion stack |
| Iterative DFS | `O(h)` -- explicit stack |
| BFS (level-order) | `O(w)` -- queue width, up to `O(n)` |
| Morris traversal | `O(1)` -- no extra space |

> For interview problems: always state whether the tree is balanced. It changes the answer from `O(n)` to `O(log n)`.

---

## Slide 20 -- Summary & Further Reading

### Key takeaways

- A binary tree is defined by the constraint of at most two children per node
- Tree shape matters: balanced gives `O(log n)`, degenerate gives `O(n)`
- Master all three DFS traversals (inorder, preorder, postorder) in both recursive and iterative forms
- Level-order (BFS) solves level-aware problems naturally
- Morris traversal achieves `O(1)` space by temporarily threading the tree
- LCA and diameter are canonical examples of post-order accumulation
- Serialisation converts structure to string and back -- essential for distributed systems
- Expression trees connect tree traversals to notation systems (infix, prefix, postfix)
- Trees are everywhere: file systems, the DOM, decision trees, compiler ASTs, database indexes

### Recommended reading

| Source | Description |
|--------|------------|
| **CLRS** | *Introduction to Algorithms* -- Chapter 12 (Binary Search Trees), Chapter 13 (Red-Black Trees) |
| **Skiena** | *The Algorithm Design Manual* -- tree traversal and applications |
| **LeetCode** | Tree tag -- 200+ problems from easy to hard |
| **Knuth** | *The Art of Computer Programming, Vol. 1* -- fundamental tree algorithms, threaded trees |
| **Sedgewick** | *Algorithms* -- BSTs, balanced search trees, applications |
