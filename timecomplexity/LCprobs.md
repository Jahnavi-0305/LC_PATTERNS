# Time Complexity Cheat Sheet

## Step 1: Count Your Loops First

| What you see | Complexity |
|---|---|
| No loops, just math/comparisons | O(1) |
| One loop through n elements | O(n) |
| One loop that halves the search space (binary search) | O(log n) |
| Two separate loops, one after another (not nested) | O(n) — you add, not multiply |
| One loop nested inside another, both go through full n | O(n²) |
| Three loops nested, all full n | O(n³) |
| Loop where each step doubles/halves the work | O(log n) |

## Step 2: Nested Loop Trap

Nested loops are usually O(n²). But not always. Ask one question:

**Does the inner loop run fully every single time the outer loop runs?**

- Yes, it fully reruns every time → O(n²)
- No, it's gated by a condition and only runs sometimes → O(n)

### How to Spot the Fake Nested Loop (Real Cost O(n))

Look for an `if` before the inner loop that blocks it most of the time:

```python
for num in numSet:
    if (num - 1) not in numSet:      # this if blocks the while most of the time
        while (num + length) in numSet:
            length += 1
```

Here's why this is O(n), not O(n²):

- The `for` loop runs n times. Cost: n.
- The `while` loop does NOT run every time. It only runs when `num` is the start of a chain (nothing right before it in the set).
- Add up every `while` step across the entire run of the program. That total can never be more than n, because every number can only get "walked over" once, by the one chain it belongs to.

So total work = n (for the for loop) + n (for all while steps combined) = O(n). You add the two, you don't multiply them.

### Quick Number Example

```
numSet = {1, 2, 3, 4, 100}

num=1   → if is True  → while runs 3 steps
num=2   → if is False → while runs 0 steps
num=3   → if is False → while runs 0 steps
num=4   → if is False → while runs 0 steps
num=100 → if is True  → while runs 1 step

Total while steps = 3 + 1 = 4, not 5×5 = 25
```

## Step 3: Watch for Hidden Loops in One-Liners

These look like O(1) but are secretly O(n):

| Code | Real cost |
|---|---|
| `str[::-1]` (reverse a string) | O(n) |
| `str1 == str2` (compare strings) | O(n) |
| `sum(arr)` | O(n) |
| `max(arr)` or `min(arr)` | O(n) |
| `x in list` | O(n) |
| `x in set` or `x in dict` | O(1) — this one is fast |
| `sorted(arr)` or `arr.sort()` | O(n log n) |
| `arr[i:j]` (slicing) | O(j−i), proportional to slice length |

## Step 4: The Golden Rule

**True nested loop — multiply:**

```
for i in range(n):        # n times
    for j in range(n):    # n times, every single time
        # O(1) work

Total = n × n = O(n²)
```

**Gated nested loop — add:**

```
for i in range(n):
    for j in range(n):
        if rare_condition:    # only true a few times, total
            do_something()

Total = O(n)
```

## Step 5: Decision Flowchart

1. Any loops at all? No → O(1). Yes → go to 2.
2. Loops nested? No, they're separate one after another → add the costs, usually O(n). Yes → go to 3.
3. Does the inner loop run fully every single outer iteration? Yes → multiply, usually O(n²). No, it's gated by a condition → add up all iterations across the whole run instead of multiplying, usually O(n).
4. Any hidden O(n) operation inside a loop (reverse, sum, sort, slice)? Yes → multiply that into your loop cost too.

## Cheat Table — Common Patterns

| Pattern | Time complexity |
|---|---|
| Two pointers (converging) | O(n) |
| Sliding window | O(n) |
| Binary search | O(log n) |
| Sorting first, then loop | O(n log n) |
| Nested loop (brute force pairs) | O(n²) |
| DFS/BFS on graph | O(V + E) |
| Backtracking (subsets/permutations) | O(2^n) or O(n!) |
| DP with 1 variable table | O(n) |
| DP with 2D table | O(n²) |
| Heap push/pop | O(log n) per operation |

## One Line to Remember

Count your loops. If nested, check if the inner loop is gated by an if. If it's gated, add instead of multiply. Watch for hidden O(n) work hiding inside one-liners like reverse, sum, sort, or `in` on a list.
