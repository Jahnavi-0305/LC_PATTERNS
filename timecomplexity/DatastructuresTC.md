# Data Structure Time Complexity — Muscle Memory Reference

## Array / List (dynamic array, like Python list)

| Operation | Average | Worst | Why |
|---|---|---|---|
| Access by index `arr[i]` | O(1) | O(1) | direct memory offset |
| Search by value `x in arr` | O(n) | O(n) | must scan every element |
| Insert at end `append` | O(1) | O(n) | usually O(1), but O(n) when array must resize/reallocate |
| Insert at beginning/middle | O(n) | O(n) | everything after shifts right |
| Delete at end `pop()` | O(1) | O(1) | no shifting needed |
| Delete at beginning/middle | O(n) | O(n) | everything after shifts left |

**Rule of thumb**: fast at the end, slow in the middle/front.

---

## Linked List

| Operation | Singly linked | Doubly linked (with tail pointer) |
|---|---|---|
| Access by index | O(n) | O(n) |
| Search by value | O(n) | O(n) |
| Insert at head | O(1) | O(1) |
| Insert at tail | O(n) — must walk to end | O(1) — tail pointer known |
| Delete at head | O(1) | O(1) |
| Delete at tail | O(n) — must find previous node | O(1) |

**Rule of thumb**: great at the ends if you have a pointer there, terrible at random access.

---

## Stack (LIFO)

| Operation | Time |
|---|---|
| Push | O(1) |
| Pop | O(1) |
| Peek/top | O(1) |
| Search for arbitrary value | O(n) |

Works the same whether backed by array or linked list — you only ever touch the top.

---

## Queue (FIFO)

| Operation | Time |
|---|---|
| Enqueue | O(1) |
| Dequeue | O(1) |
| Peek/front | O(1) |
| Search for arbitrary value | O(n) |

Note: a naive array-based queue (popping from front with `list.pop(0)`) is actually O(n) in Python — use `collections.deque` for true O(1) both ends.

---

## Deque (double-ended queue)

| Operation | Time |
|---|---|
| Add/remove from front | O(1) |
| Add/remove from back | O(1) |
| Access by index | O(n) |

---

## Hash Table / Dictionary / Set

| Operation | Average | Worst |
|---|---|---|
| Search (`x in dict/set`) | O(1) | O(n) |
| Insert | O(1) | O(n) |
| Delete | O(1) | O(n) |

Worst case O(n) only happens with heavy hash collisions (rare in practice, and Python's hash randomization protects against adversarial cases). For everyday reasoning, treat these as O(1).

---

## Binary Search Tree (balanced, e.g. AVL/Red-Black)

| Operation | Average | Worst (unbalanced tree) |
|---|---|---|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

Worst case happens when the tree degenerates into a straight line (like inserting sorted data into a plain BST with no rebalancing).

---

## Heap (Binary Heap — min-heap or max-heap)

| Operation | Time |
|---|---|
| Peek min/max | O(1) |
| Insert (push) | O(log n) |
| Remove min/max (pop) | O(log n) |
| Search arbitrary value | O(n) |
| Build heap from n elements | O(n) |

Heaps are great when you always need the smallest/largest element fast, but bad for looking up random values.

---

## Trie (prefix tree)

| Operation | Time |
|---|---|
| Insert word | O(L) — L = length of word |
| Search word | O(L) |
| Search prefix | O(L) |

Independent of how many words are stored — only depends on the length of the word/prefix itself.

---

## Graph

| Operation | Adjacency list | Adjacency matrix |
|---|---|---|
| Add edge | O(1) | O(1) |
| Remove edge | O(E) worst case (find it first) | O(1) |
| Check if edge exists | O(V) worst case | O(1) |
| Traverse all edges (DFS/BFS) | O(V + E) | O(V²) |
| Space | O(V + E) | O(V²) |

V = number of vertices, E = number of edges. Use adjacency list for sparse graphs (most real-world graphs), matrix for dense graphs or when you need O(1) edge lookups.

---

## Quick Comparison Table

| Structure | Access | Search | Insert | Delete |
|---|---|---|---|---|
| Array | O(1) | O(n) | O(n) | O(n) |
| Linked list | O(n) | O(n) | O(1) | O(1) |
| Stack | O(n) | O(n) | O(1) | O(1) |
| Queue | O(n) | O(n) | O(1) | O(1) |
| Hash table/set | N/A | O(1) avg | O(1) avg | O(1) avg |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) |
| Heap | O(1) min/max only | O(n) | O(log n) | O(log n) |
| Trie | O(L) | O(L) | O(L) | O(L) |

*(Access for stack/queue/linked list means "arbitrary index access," not access to the natural end — those ends are O(1).)*

---

## The One Thing to Actually Memorize

- **Need fast random access by index?** → array
- **Need fast insert/delete at known ends?** → linked list, stack, queue, deque
- **Need fast lookup by value/key, don't care about order?** → hash table/set
- **Need sorted order AND fast search/insert/delete?** → balanced BST
- **Need the min or max repeatedly?** → heap
- **Need fast prefix matching on strings?** → trie
- **Need to model connections/relationships?** → graph (list for sparse, matrix for dense)

Everything else on this sheet is just filling in the "why" behind these seven bullets.
