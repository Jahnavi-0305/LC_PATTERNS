## Static Template to Memorize (4 Chunks)

**1. Setup — queue starts with source, mark it visited immediately**
```python
from collections import deque
queue = deque([start])
visited = {start}
```

**2. Outer loop — keep going while queue has nodes**
```python
while queue:
```

**3. Level loop — process exactly one full layer at a time**
```python
    for _ in range(len(queue)):
        node = queue.popleft()
```

**4. Expand — check neighbors, mark visited on ADD (not on pop)**
```python
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

## The One-Line Mental Model

Pop the front, check its neighbors, add unvisited ones to the back — repeat until the queue is empty.

## Where the Code Changes Table

| # | Question to ask | What it means | Example |
|---|---|---|---|
| 1 | Where does the queue start? | one node, or multiple nodes at once | single source vs multi-source (e.g., all rotten oranges added together) |
| 2 | Do I need the level/step counter? | only if the problem asks "how many steps/minutes/levels" | `level += 1` after the inner for loop, or skip it entirely |
| 3 | What do I collect per level? | nothing, just a counter, or a list of values | `result.append(level_values)` for level-order traversal |
| 4 | How do I mark visited? | in-place mutation, a set, or a distance/steps array | set() for simple reachability, array if you need to store distance too |
| 5 | What's the "neighbor" logic? | 4-directional grid, adjacency list, or something custom | grid moves vs `graph[node]` vs word-ladder-style transformations |

## When to Use This Table

Ask yourself the 5 questions above whenever you hit a new BFS problem — the queue-and-level skeleton itself never changes, only these 5 things adjust based on what the problem is actually asking for.
