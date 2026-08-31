# Tree Traversal Patterns (DFS & BFS) — Zero to Hero

## When Should Tree Traversal Click in Your Mind?

Think Trees when you see:

- Input is a **binary tree / binary search tree / n-ary tree** (`TreeNode`, `root`)
- Keywords like "level", "depth", "height", "path", "ancestor", "subtree", "serialize"
- You need to **visit every node** to compute something (sum, count, max depth, validate)
- You need to **build, copy, or compare** trees
- You need to process nodes **level by level** (closest to root first) → BFS
- You need to process nodes **root-to-leaf** or **leaf-to-root** in some order → DFS

The single biggest confusion beginners have: *"It's always just three lines moved around — so why do preorder/inorder/postorder give different results for different problems?"*
The answer is: **the order you visit nodes changes what information is available to you at each step.** That's the whole game. Once you internalize *why* each order exists, you'll instantly know which one to reach for.

---

## The Real Difference Between Pre / In / Post (Read This First)

All three are the exact same recursive skeleton:

```python
def dfs(node):
    if not node:
        return
    # (1) do something with node.val
    dfs(node.left)
    # (2) do something with node.val
    dfs(node.right)
    # (3) do something with node.val
```

The **only** thing that changes is *where* you put your logic relative to the two recursive calls. But that placement completely changes what you know at that point:

| Traversal | Order | What you know at that moment |
|---|---|---|
| **Preorder** (node → left → right) | Process node **before** visiting children | You know nothing about children yet. Good for **passing information DOWN** (parent → child), or when you need to **create/copy** the node before its children exist. |
| **Inorder** (left → node → right) | Process node **between** left and right subtree | For a BST, this visits nodes in **sorted order**. Good for anything involving **sorted sequence** of values. |
| **Postorder** (left → right → node) | Process node **after** visiting both children | You already have the **fully computed results from both children**. Good for **passing information UP** (child → parent), like heights, sums, or "is this subtree valid?". |

**The one question that resolves 90% of confusion:**

> "Do I need info from my children before I can process myself (→ postorder), or do I need to pass info from my parent down to my children (→ preorder), or do I specifically care about sorted/BST order (→ inorder)?"

If the answer is "I need my children's answers first" → **Postorder**.
If the answer is "I need to hand something down before recursing" → **Preorder**.
If the answer is "I care about left-to-right sorted order" → **Inorder**.

**A note on DFS vs. BFS here:** Pre/in/post order are **DFS orderings by definition** — they only exist because DFS goes all the way down one branch before backtracking. There's no "BFS version" of pre/in/post; BFS (queue) can only ever produce **level order** (Pattern 12), a fundamentally different traversal. What *does* vary for pre/in/post is **how** you do the DFS: recursion (the call stack does the work for you, implicitly) or an explicit stack you manage yourself (iterative). Both are still DFS — you'll see the iterative/stack version included alongside each recursive template below, since interviewers love asking "now without recursion."

---

## Pattern 12: Level Order Traversal (BFS)

**When to use:**
- You need to process nodes **level by level** (row by row)
- Keywords: "level order", "shortest path in a tree/grid", "right side view", "zigzag", "minimum depth"

**Key idea:**
Use a **queue**. Push the root. On each iteration, record how many nodes are currently in the queue (`len(queue)`) — that's exactly one level. Pop that many nodes, process them, and push their children. This cleanly separates levels without needing extra depth tracking.

**Template:**
```python
from collections import deque

def level_order(root):
    if not root:
        return []

    result = []
    queue = deque([root])

    while queue:
        level_size = len(queue)   # number of nodes at this level
        level_vals = []

        for _ in range(level_size):
            node = queue.popleft()
            level_vals.append(node.val)

            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

        result.append(level_vals)

    return result
```

**Why it works:** A queue is FIFO — nodes are processed in the exact order they were discovered, which is naturally level by level. Snapshotting `len(queue)` before the inner loop is what lets you know exactly where one level ends and the next begins.

**Problems:**
- [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
- [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
- [Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

---

## Pattern 13: Recursive Preorder Traversal (Node → Left → Right)

**When to use:**
- You need to **create a copy** of the tree, or **serialize** it (process a node before you touch its children)
- You need to **pass information down** from parent to child (e.g., current path, running sum, current depth)
- Common phrasing: "path from root to leaf", "sum of root-to-leaf paths", "flatten the tree"

**Key idea:**
Process the current node **first**, then recurse into left, then right. This means at the moment you process a node, you already have everything you needed *from above* (passed down as a function argument), but nothing yet *from below*.

**The core template — this is preorder, memorize this:**
```python
def preorder(node, result):
    if not node:
        return

    result.append(node.val)   # (1) process node — happens BEFORE recursing
    preorder(node.left, result)
    preorder(node.right, result)
```

**Variant — combined with path-tracking (for root-to-leaf path problems):**
This is *not* a different kind of preorder — it's the same node → left → right order, with path-building and backtracking layered on top because the problem needs the running path at each step.
```python
def preorder_paths(node, path, result):
    if not node:
        return

    path.append(node.val)          # process node — using info passed from parent

    if not node.left and not node.right:   # leaf node
        result.append(list(path))

    preorder_paths(node.left, path, result)
    preorder_paths(node.right, path, result)

    path.pop()   # backtrack — remove node before returning to parent
```

**Why it works:** Because you process the node before recursing, anything you pass as an argument (like `path` or a running sum) reflects the state **from the root down to this node** — perfect for path-based problems.

**Iterative version (explicit stack, common follow-up in interviews):**
```python
def preorder_iterative(root):
    if not root:
        return []

    result = []
    stack = [root]

    while stack:
        node = stack.pop()
        result.append(node.val)          # process node — same "before children" moment

        # push right FIRST so left is processed first (stack is LIFO)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)

    return result
```

**Problems:**
- [Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)
- [Path Sum](https://leetcode.com/problems/path-sum/)
- [Path Sum II](https://leetcode.com/problems/path-sum-ii/)
- [Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)
- [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

---

## Pattern 14: Recursive Inorder Traversal (Left → Node → Right)

**When to use:**
- The tree is a **Binary Search Tree (BST)** and you care about **sorted order**
- Common phrasing: "kth smallest", "validate BST", "convert BST to sorted list"

**Key idea:**
Fully process the left subtree, **then** the current node, **then** the right subtree. For a BST, this guarantees you visit nodes in strictly increasing order — because everything smaller than the node lives in its left subtree, and everything bigger lives in its right subtree.

**Template:**
```python
def inorder(node, result):
    if not node:
        return

    inorder(node.left, result)
    result.append(node.val)     # process node — between left and right
    inorder(node.right, result)
```

**Iterative version (common follow-up in interviews):**
```python
def inorder_iterative(root):
    result = []
    stack = []
    curr = root

    while curr or stack:
        while curr:              # go as far left as possible
            stack.append(curr)
            curr = curr.left

        curr = stack.pop()       # process node
        result.append(curr.val)
        curr = curr.right        # then go right
```

**Why it works:** In a BST, "left < node < right" holds at every node. Visiting left-node-right recursively therefore produces values in fully sorted order — this is the *only* traversal order with that guarantee.

**Problems:**
- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
- [Convert BST to Sorted Doubly Linked List](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/)

---

## Pattern 15: Recursive Postorder Traversal (Left → Right → Node)

**When to use:**
- You need info **from both children** before you can compute the answer for the current node
- Common phrasing: "height of tree", "diameter", "is balanced", "sum of subtree", "delete nodes and return forest"

**Key idea:**
Fully process left, fully process right, **then** use their results to compute the current node's answer. This is the "bottom-up" pattern — children finish their work and hand the result up to the parent.

**Template:**
```python
def postorder(node):
    if not node:
        return 0   # base case value depends on the problem (0 for sum/height, True for validity, etc.)

    left_result = postorder(node.left)
    right_result = postorder(node.right)

    # (1) now combine children's results with the current node
    current_result = 1 + max(left_result, right_result)   # example: height

    return current_result
```

**Why it works:** Recursion naturally resolves the deepest nodes first, so by the time you're back at a parent node, both `left_result` and `right_result` are fully computed — exactly what you need for bottom-up aggregation.

**Iterative version (explicit stack, common follow-up in interviews):**
```python
def postorder_iterative(root):
    if not root:
        return []

    result = []
    stack = [root]

    while stack:
        node = stack.pop()
        result.append(node.val)     # collect in "node, right, left" order

        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)

    return result[::-1]             # reverse to get "left, right, node"
```
*(Trick: this produces the reverse of postorder using a preorder-like scan, then flips the result — much simpler than trying to track "have I visited both children yet" with a stack.)*

**Problems:**
- [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)
- [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
- [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/)

---

## Pattern 16: Lowest Common Ancestor (LCA)

**When to use:**
- You need to find the **deepest node that is an ancestor of both** of two given nodes
- Common phrasing: "lowest common ancestor", "find the split point between two paths"

**Key idea:**
This is a special **postorder** pattern. Recurse into left and right. If a node finds `p` or `q` in its left subtree AND its right subtree, **that node is the LCA**. If only one side finds something, pass that result up (it might be the LCA higher up, or it might just be `p`/`q` itself waiting to meet its pair).

**Template (general binary tree):**
```python
def lowest_common_ancestor(node, p, q):
    if not node or node == p or node == q:
        return node

    left = lowest_common_ancestor(node.left, p, q)
    right = lowest_common_ancestor(node.right, p, q)

    if left and right:
        return node       # p and q found on different sides — this node is the LCA

    return left if left else right   # bubble up whichever side found something
```

**BST version (faster, uses BST ordering instead of full postorder search):**
```python
def lowest_common_ancestor_bst(node, p, q):
    while node:
        if p.val < node.val and q.val < node.val:
            node = node.left
        elif p.val > node.val and q.val > node.val:
            node = node.right
        else:
            return node   # split point found
```

**Why it works:** Postorder lets each node ask "did I find `p` or `q` on my left? On my right?" only *after* both subtrees have already searched — the first node where both answers are "yes" must be the lowest (deepest) common ancestor, because any node further up would also technically be an ancestor, but not the lowest one.

**Problems:**
- [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
- [Lowest Common Ancestor of a Binary Tree III](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/)

---

## Pattern 17: Serialization and Deserialization

**When to use:**
- You need to **convert a tree into a string** (to save/transmit it) and **rebuild it back** from that string
- Common phrasing: "serialize and deserialize", "design an algorithm to encode/decode a tree"

**Key idea:**
Use **preorder** traversal to serialize — write the node's value first, then recurse left, then right — and mark `null` children explicitly with a placeholder (like `"#"` or `"N"`). Because preorder always writes "parent before children," deserializing is simple: read one value at a time in the *same order* you wrote them, and recursively rebuild.

**Template:**
```python
class Codec:
    def serialize(self, root):
        result = []

        def preorder(node):
            if not node:
                result.append("N")   # marker for null
                return
            result.append(str(node.val))
            preorder(node.left)
            preorder(node.right)

        preorder(root)
        return ",".join(result)

    def deserialize(self, data):
        values = iter(data.split(","))

        def build():
            val = next(values)
            if val == "N":
                return None
            node = TreeNode(int(val))
            node.left = build()     # same order as serialize: node, then left, then right
            node.right = build()
            return node

        return build()
```

**Why it works:** Preorder writes each node *before* its children, so when reading back, the very first token you see is always the root of whatever subtree you're currently rebuilding — you never have to look ahead or backtrack, you just consume tokens in order.

**Problems:**
- [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)
- [Serialize and Deserialize BST](https://leetcode.com/problems/serialize-and-deserialize-bst/)
- [Serialize and Deserialize N-ary Tree](https://leetcode.com/problems/serialize-and-deserialize-n-ary-tree/)

---

## The Decision Flowchart (Use This Every Time You're Stuck)

Ask these questions **in order**:

1. **Does the problem mention "level", "row", "shortest path in unweighted tree/grid", or "closest to root"?**
   → **BFS / Level Order** (Pattern 12)

2. **Do I need the answer from BOTH children before I can compute the answer for the current node?**
   (heights, sums of subtrees, "is balanced", diameter, LCA)
   → **Postorder** (Pattern 15 or 16)

3. **Do I need to pass information DOWN from parent to child** (a running path, a running sum, a target that shrinks as you go)?
   → **Preorder** (Pattern 13)

4. **Is it a BST and do I care about sorted order** (kth smallest, validate BST, convert to sorted list)?
   → **Inorder** (Pattern 14)

5. **Do I need to rebuild the tree from a string, or turn the tree into a string?**
   → **Preorder-based Serialization** (Pattern 17)

If more than one seems to apply, default to **postorder** — "gather info from children first" is the most common real interview pattern once problems get past basic traversal.

---

## Common Mistakes to Avoid

❌ Trying to compute a "bottom-up" value (height, sum, balance) using **preorder**
✅ Bottom-up values almost always need **postorder** — you can't know a child's answer before it's computed

❌ Using inorder on a tree that **isn't a BST**, expecting sorted output
✅ Inorder only gives sorted order specifically because of the BST property (`left < node < right`) — on a regular binary tree it's just "some" order

❌ Forgetting to **backtrack** (pop from path/list) after recursing in path-based preorder problems
✅ Always undo what you added to a shared list/path before returning to the parent call — otherwise siblings will see stale data

❌ In Level Order BFS, forgetting to snapshot `len(queue)` before the inner loop
✅ If you check `len(queue)` inside the loop after already pushing children, you'll mix levels together

❌ In LCA, assuming you need to explicitly store paths to `p` and `q` and compare them
✅ The postorder "does left have it AND does right have it" trick avoids needing extra path storage entirely

❌ In Serialization, using inorder instead of preorder
✅ Inorder alone **cannot** be uniquely deserialized back into a tree — you need preorder (or preorder + inorder together) because preorder always tells you the root first

❌ Thinking BFS (queue) can produce a preorder/inorder/postorder result
✅ Those three are DFS orderings by definition — a queue can only give you level order. The stack-vs-recursion choice only decides *how* you do DFS, not whether you get pre/in/post

---

## Must-Solve Tree Traversal Questions (In Order)

**Easy**
- [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- [Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)
- [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

**Medium**
- [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
- [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
- [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

**Hard**
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

---

## Final Tip (Interview Gold)

If you can answer:

**"Do I need my children's results before I act (postorder), do I need to pass something down before I recurse (preorder), or do I need sorted order (inorder) — or is this about rows, not depth, at all (BFS)?"**

👉 That single question tells you exactly which traversal to reach for, every time.

Happy Coding 🚀
