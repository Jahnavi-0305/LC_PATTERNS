## Static Template to Memorize (4 Chunks)

**1. Setup — build graph + tracking structures**
```python
graph = defaultdict(list)
for course, prereq in prerequisites:
    graph[course].append(prereq)
visiting = set()
visited = set()
order = []
```

**2. Guard clause — check states first**
```python
if node in visiting:
    return False
if node in visited:
    return True
```

**3. Mark visiting → recurse → backtrack → mark visited → record order**
```python
visiting.add(node)
for neighbor in graph[node]:
    if not dfs(neighbor):
        return False
visiting.remove(node)
visited.add(node)
order.append(node)
return True
```

**4. Outer loop — run dfs on every node, stop early on cycle**
```python
for course in range(numCourses):
    if not dfs(course):
        return []
return order
```

## The One-Line Mental Model

Cycle-detect like before, but every time a node fully finishes (no cycle below it), append it to the order list — dependencies always get appended before the things depending on them.

## Where the Code Changes (From Cycle Detection Template)

| # | What changes | Cycle Detection only | Topological Sort |
|---|---|---|---|
| 1 | Extra data structure | none | `order = []` list |
| 2 | New line inside dfs | none | `order.append(node)` right after marking visited |
| 3 | Return value on cycle found | `False` | `[]` (empty list) |
| 4 | Final return value | `True` | `order` (the built list) |

## The Single Thing to Remember

Everything is identical to your Cycle Detection template — the ONLY new line is `order.append(node)`, placed right after `visited.add(node)`, before `return True`. If you already memorized cycle detection, topo sort is just that one extra line.
