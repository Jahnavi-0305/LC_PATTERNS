Got it — let's slow way down and go through the code like it's your first time ever seeing recursion or grids. No jargon left unexplained.

## Setting the Scene

Imagine the grid is like a piece of graph paper. Each little square is either land ('1') or water ('0'). You're standing above it looking down, and you need to count how many separate land blobs (islands) exist. [dev](https://dev.to/stuttskl/solving-number-of-islands-using-a-depth-first-search-6f3)

## The Code, Piece by Piece

```python
def numIslands(grid):
    if not grid:
        return 0
```
This just checks: "is the grid empty?" If someone gave you nothing, there are 0 islands. Nothing fancy — just a safety check. [colorfulcodesblog.wordpress](https://colorfulcodesblog.wordpress.com/2018/09/06/number-of-islands-tutorial-python/)

```python
    rows, cols = len(grid), len(grid[0])
    count = 0
```
- `rows` = how many rows the grid has (count from top to bottom).
- `cols` = how many columns it has (count left to right).
- `count` = your tally of islands found so far. It starts at 0 because you haven't found any yet. [colorfulcodesblog.wordpress](https://colorfulcodesblog.wordpress.com/2018/09/06/number-of-islands-tutorial-python/)

```python
    def dfs(r, c):
```
This defines a helper function named `dfs` (short for Depth-First Search). Think of `dfs` as "a little explorer" you send into the grid. You give it a coordinate `(r, c)` — a row and column number — and it goes and explores from there. [hellointerview](https://www.hellointerview.com/learn/code/depth-first-search/number-of-islands)

```python
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
```
This is the "stop" condition — the explorer checks 5 things before doing anything:
- Am I above the grid (r < 0)? Stop.
- Am I below the grid (r >= rows)? Stop.
- Am I left of the grid (c < 0)? Stop.
- Am I right of the grid (c >= cols)? Stop.
- Is this spot NOT land (not '1')? Stop.

If any of these is true, the explorer just turns around and does nothing (`return`). This protects you from checking squares that don't exist and from re-exploring water or already-visited land. [algo](https://algo.monster/liteproblems/200)

```python
        grid[r][c] = '0'
```
If none of those stop conditions triggered, it means we're standing on unvisited land. So we immediately mark it as water ('0'). This is like "planting a flag" — it says "I've been here, don't count me again". We don't need a separate "visited" tracker because we're just overwriting the grid itself. [algo](https://algo.monster/liteproblems/200)

```python
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)
```
Now the explorer sends 4 clones of itself to check the 4 neighboring squares: one row down, one row up, one column right, one column left. Each clone runs the exact same function again — checks the stop conditions, marks land as visited, sends 4 more clones, and so on. This chain reaction is what "flood-fills" the entire island. [hellointerview](https://www.hellointerview.com/learn/code/depth-first-search/number-of-islands)

```python
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
```
This is the main scanner. It walks through every row, and inside every row, every column — like reading a book left to right, line by line. Every time it spots a '1' that's still land (meaning DFS hasn't sunk it yet), that's proof of a brand-new island, so `count += 1`. Then it calls `dfs(r, c)` to sink that entire island so we never count it again. [algomaster](https://algomaster.io/learn/dsa/number-of-islands)

```python
    return count
```
After scanning the whole grid, whatever `count` ended up as is your final answer. [algomaster](https://algomaster.io/learn/dsa/number-of-islands)

## The Big Picture 

1. Walk across every square of the grid.
2. Whenever you find land you haven't seen before → that's a NEW island, add 1 to your counter.
3. Immediately send your "explorer" (dfs) to sink that entire island so it turns to water, so you never accidentally count it twice.
4. Keep walking until you've checked every square.
5. Whatever number you counted is your final answer.

That's genuinely the whole trick — the DFS function is just "spread through connected land and mark it visited". [geeksforgeeks](https://www.geeksforgeeks.org/dsa/find-the-number-of-islands-using-dfs/)
