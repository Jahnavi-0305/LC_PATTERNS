# Linked Lists for Design Problems — LRU Cache, Skip List & Everything You Need

## Why This Guide Exists

Design problems (LRU Cache, LFU Cache, Skip List, Design a Browser History, etc.) don't need fancy linked list *algorithms* — no reversing, no cycle detection tricks. They need you to be **comfortable and fast** with the basic moves: create a node, connect nodes, insert one, remove one, walk through the list. That's genuinely it. This guide is just those basics, done properly, so design problems stop feeling scary.

---

## Part 1: The Absolute Basics (Refresh This First)

### What a node actually is

A linked list isn't one "thing" — it's a bunch of separate little boxes (**nodes**), where each box holds a value and a **pointer** (an arrow) to the next box.

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.next = None   # this arrow starts pointing at nothing
```

That's the entire building block. Everything else is just connecting these boxes together correctly.

### Building a tiny list by hand

Let's manually build `1 -> 2 -> 3`:

```python
a = Node(1)
b = Node(2)
c = Node(3)

a.next = b   # box 1's arrow now points to box 2
b.next = c   # box 2's arrow now points to box 3
# c.next stays None — it's the last box
```

If you draw this on paper as three boxes with arrows, it'll click faster than reading code. Do that once, seriously — it helps.

### Walking through a list (traversal)

This is the move you'll use constantly — visiting every node one at a time:

```python
def print_list(head):
    curr = head          # start at the beginning
    while curr:           # keep going until we fall off the end (None)
        print(curr.val)
        curr = curr.next  # step to the next box
```

**The pattern to memorize:** `curr = head`, then `while curr:`, then `curr = curr.next` at the end. This exact shape shows up in almost every linked list problem you'll ever write.

### Inserting a node

**At the front (easiest):**
```python
def insert_at_head(head, val):
    new_node = Node(val)
    new_node.next = head   # new node points to the old first node
    return new_node          # new node IS the new head now
```

**After a given node (common in design problems):**
```python
def insert_after(node, val):
    new_node = Node(val)
    new_node.next = node.next   # new node points to whatever came after `node`
    node.next = new_node        # `node` now points to the new node
```
Order matters here — you must save `node.next` into the new node **before** you overwrite `node.next`, or you'll lose the rest of the list.

### Deleting a node

You can't actually "remove" a box in the middle by talking to the box itself — you have to tell the box **before it** to skip over it:

```python
def delete_after(node):
    if node.next:
        node.next = node.next.next   # skip over the next node entirely
```

The skipped node still exists in memory for a moment, but nothing points to it anymore, so it's effectively gone.

### Searching / "get" from a linked list

Unlike an array, you **cannot** jump straight to index 5 — you have to walk there one node at a time:

```python
def get(head, target_val):
    curr = head
    while curr:
        if curr.val == target_val:
            return curr
        curr = curr.next
    return None   # not found
```

This is the single biggest thing to remember for design problems: **plain linked lists are O(n) to search.** That's exactly *why* LRU Cache and Skip List exist — they're clever tricks to make search faster while keeping the "easy insert/delete" superpower of linked lists.

---

## Part 2: The One Upgrade You Need — Doubly Linked Lists

A regular ("singly") linked list only has `.next`. For design problems, you almost always want a **doubly** linked list — each node also has a `.prev` arrow pointing backward.

```python
class DNode:
    def __init__(self, val):
        self.val = val
        self.prev = None
        self.next = None
```

**Why this matters so much:** in a singly linked list, deleting a node in the middle means you first have to *walk from the head* just to find the node *before* it — that's slow. In a doubly linked list, a node already knows its own `prev`, so you can remove it in **O(1)**, instantly, no walking required. This one upgrade is what makes LRU Cache possible.

**Removing a node from a doubly linked list (the move you'll use constantly):**
```python
def remove(node):
    node.prev.next = node.next   # the node before skips over `node`
    node.next.prev = node.prev   # the node after also skips over `node`
    # `node` is now fully disconnected from the list
```

**Inserting a node right after a given node:**
```python
def insert_after(node, new_node):
    next_node = node.next        # (0) SAVE this first — this is the "don't cut yet" step

    new_node.prev = node         # (1) wire the new node backward
    new_node.next = next_node    # (2) wire the new node forward — safe, we saved it in step 0

    next_node.prev = new_node    # (3) rewire the old next node to point back to new_node

    node.next = new_node         # (4) FINALLY cut/overwrite node.next — do this LAST
```

**The rule that makes this order obvious:** never overwrite a pointer until you've already used its old value everywhere you still need it. `node.next` is the dangerous one here — it's your only way to reach the old next node, so it gets touched last, after everything else is safely wired up.

**A trick worth knowing:** most people use two **dummy nodes** — a fake `head` and fake `tail` that never hold real data — just so you never have to special-case "what if the list is empty" or "what if I'm removing the first/last real node." You'll see this below.

---

## Part 3: LRU Cache — Putting It All Together

**What it needs to do:** `get(key)` and `put(key, value)`, both in **O(1)** time, while remembering which key was used **least recently** so it can be evicted when the cache is full.

**Why a linked list alone isn't enough:** a linked list is great for "move this to the front instantly" (O(1), once you have the node) — but *finding* the node by key would be O(n) search. So we pair it with a **hash map** for instant lookup, and a **doubly linked list** for instant reordering/removal. This combo is the whole trick.

**The mental model:**
- Hash map: `key -> node` (instant lookup — "where is this node?")
- Doubly linked list: keeps nodes ordered from **most recently used** (near head) to **least recently used** (near tail)
- On `get`: find the node via the hash map, then move it to the front (it's now the most recently used)
- On `put`: same idea, and if the cache is over capacity, remove the node at the tail (the least recently used one)

```python
class Node:
    def __init__(self, key, val):
        self.key = key      # we store the key too — needed when evicting from the tail
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}     # key -> Node

        # dummy head and tail so we never special-case empty/edge cases
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _insert_at_front(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key):
        if key not in self.cache:
            return -1

        node = self.cache[key]
        self._remove(node)
        self._insert_at_front(node)   # mark as most recently used
        return node.val

    def put(self, key, value):
        if key in self.cache:
            self._remove(self.cache[key])

        node = Node(key, value)
        self.cache[key] = node
        self._insert_at_front(node)

        if len(self.cache) > self.capacity:
            lru = self.tail.prev          # the real node just before the dummy tail
            self._remove(lru)
            del self.cache[lru.key]
```

**Why it works:** every operation — lookup (hash map), move-to-front (doubly linked list), evict-from-tail (doubly linked list) — is O(1) on its own, so the whole thing stays O(1) even though it feels like "a lot is happening."

**Problems:**
- [LRU Cache](https://leetcode.com/problems/lru-cache/)
- [LFU Cache](https://leetcode.com/problems/lfu-cache/) *(same idea, with an extra layer for frequency)*
- [Design Browser History](https://leetcode.com/problems/design-browser-history/) *(simpler — doubly linked list without the hash map trick)*
- [Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)

---

## Part 4: Skip List — The "Express Lanes" Trick

**What it needs to do:** `search`, `add`, and `erase`, ideally faster than the O(n) you get from a plain linked list — without needing a balanced tree.

**The idea, in plain English:** imagine a regular sorted linked list, but boring to search because you have to check every single node. Now imagine you add a **second row above it** that only contains *some* of the nodes — like an express train that skips most stops. You search the express row first to get close to where you need to be, then drop down to the normal row to find the exact spot. Add a third row above that with even fewer stops, and you can skip even further, even faster.

That's a skip list: **multiple stacked linked lists**, where higher levels have fewer nodes and let you skip large chunks at once — giving you roughly O(log n) search, similar in spirit to binary search, but built entirely out of linked lists instead of an array.

**Each node needs pointers to the next node at *every level it appears on*:**
```python
import random

class SkipNode:
    def __init__(self, val, level):
        self.val = val
        self.forward = [None] * (level + 1)   # one "next" pointer per level this node lives on
```

**Searching (start at the top level, drop down when you overshoot):**
```python
class SkipList:
    def __init__(self, max_level=16):
        self.max_level = max_level
        self.head = SkipNode(-1, max_level)   # dummy head that exists on every level

    def search(self, target):
        curr = self.head

        # start at the TOP level and work down
        for level in range(self.max_level, -1, -1):
            while curr.forward[level] and curr.forward[level].val < target:
                curr = curr.forward[level]   # move forward on this level

        curr = curr.forward[0]   # drop to the bottom (normal) level for the exact node
        return curr is not None and curr.val == target
```

**Why it works:** each level you drop down from lets you skip past a chunk of nodes you already know don't matter — exactly like Pattern 59's binary search cutting the search space in half, except here it's done by "how tall a node happens to be" (usually decided randomly when it's inserted) instead of array indices.

**You almost never need to hand-build a full skip list from scratch in an interview** — but knowing *why* it's just "linked lists stacked in layers" is what Pattern 62 is to Skip List. If you get the `search` idea above, you're 90% of the way there.

**Problems:**
- [Design Skiplist](https://leetcode.com/problems/design-skiplist/)

---

## Common Mistakes to Avoid

❌ Overwriting a `.next` pointer before saving what it used to point to
✅ Always save the "old" pointer into your new node **first**, then rewire — otherwise you lose the rest of the list permanently

❌ Forgetting to update **both** `.prev` and `.next` when removing a node from a doubly linked list
✅ Both neighbors need to skip over the removed node — updating only one side leaves the list broken

❌ Special-casing "the list is empty" or "removing the first/last node" over and over
✅ Use dummy head/tail nodes (like in the LRU Cache example) — they make every insert/remove follow the exact same code path, no exceptions needed

❌ In LRU Cache, forgetting to store the `key` inside the node itself
✅ When you evict the tail node, you need its key to also delete it from the hash map — you can't get a dictionary key back from just the value

❌ Thinking you need to memorize skip list insert/delete code cold
✅ Understanding *why* it's a stack of linked lists with fewer nodes per level (the "express lane" idea) matters far more than memorizing the exact insertion code

---

## Must-Solve Questions (In Order)

**Easy — pure fundamentals warm-up (not design, just get comfortable first)**
- [Design Linked List](https://leetcode.com/problems/design-linked-list/) *(do this before anything else — it's literally Part 1 and Part 2 of this guide, turned into one problem)*

**Medium**
- [LRU Cache](https://leetcode.com/problems/lru-cache/)
- [Design Browser History](https://leetcode.com/problems/design-browser-history/)
- [Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)
- [Design Skiplist](https://leetcode.com/problems/design-skiplist/)

**Hard**
- [LFU Cache](https://leetcode.com/problems/lfu-cache/)

---

## Final Tip

If you can answer:

**"Do I need instant lookup by key (hash map), instant reordering/removal (doubly linked list), or both?"**

👉 That tells you exactly how to combine a hash map with a linked list — which is the trick behind almost every linked-list design problem you'll see.

Happy Coding 🚀
