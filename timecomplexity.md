```markdown
# Time Complexity Cheat Sheet — How to Identify Big-O

## Step 1: Count Your Loops FIRST

| What you see | Complexity |
|---|---|
| No loops, just math/comparisons | O(1) |
| One loop through n elements | O(n) |
| One loop that halves the search space (binary search) | O(log n) |
| Two SEPARATE loops (not nested, one after another) | O(n) — they ADD, not multiply |
| One loop nested inside another (both go through full n) | O(n²) |
| Three loops nested (all full n) | O(n³) |
| Loop where each step doubles/halves work | O(log n) |

## Step 2: The "Nested Loop = O(n²)" TRAP

Nested loops are USUALLY O(n²) — but not always. Ask this question:

> "Does the inner loop reset and run fully EVERY single time the outer loop runs?"

- YES, it fully reruns every time → O(n²) (true nested loop)
- NO, it's gated by a condition and only runs occasionally → O(n) (amortized)

### How to spot the "fake nested loop" (amortized O(n))
Look for an IF condition BEFORE the inner loop that skips most iterations:
```python
for num in numSet:
    if (num - 1) not in numSet:   # ← this gate makes it O(n), not O(n²)
        while (num + length) in numSet:
            length += 1
```
RULE: If there's a gatekeeping IF before the inner loop, add up all loop
work across the WHOLE run, don't multiply per outer iteration.

## Step 3: Watch for HIDDEN loops disguised as one-liners

These LOOK like O(1) but are secretly O(n):
| Code | Real Cost |
|---|---|
| `str[::-1]` (reverse a string) | O(n) |
| `str1 == str2` (compare strings) | O(n) |
| `sum(arr)` | O(n) |
| `max(arr)` or `min(arr)` | O(n) |
| `x in list` | O(n) |
| `x in set` or `x in dict` | O(1) ✅ (this one IS fast) |
| `sorted(arr)` or `arr.sort()` | O(n log n) |
| `arr[i:j]` (slicing) | O(j-i) — proportional to slice length |

## Step 4: The Golden Rule for Nested Loops

```
for i in range(n):        # outer: n times
    for j in range(n):    # inner: n times, EVERY time
        # O(1) work here

Total = n × n = O(n²)
```

```
for i in range(n):           # outer: n times
    for j in range(n):
        if rare_condition:   # only true a few times total
            do_something()

Total = O(n)  ← because rare_condition rarely triggers
```

## Step 5: Quick Decision Flowchart

```
1. Any loops at all?
   NO  → O(1)
   YES → go to 2

2. Loops nested?
   NO (separate, one after another) → ADD their costs (usually O(n))
   YES → go to 3

3. Does inner loop run FULLY every outer iteration?
   YES → MULTIPLY costs (usually O(n²))
   NO (gated by condition) → total ALL loop iterations across
        the whole program, don't multiply (usually O(n))

4. Any hidden O(n) operations inside loops? (reverse, sum, sort, slice)
   YES → multiply that into your loop cost too
```

## Cheat Table — Common Patterns and Their Complexity

| Pattern | Time Complexity |
|---|---|
| Two Pointers (Converging) | O(n) |
| Sliding Window | O(n) |
| Binary Search | O(log n) |
| Sorting first, then loop | O(n log n) |
| Nested loop (brute force pairs) | O(n²) |
| DFS/BFS on graph | O(V + E) |
| Backtracking (subsets/permutations) | O(2^n) or O(n!) |
| DP with 1 variable table | O(n) |
| DP with 2D table | O(n²) |
| Heap push/pop | O(log n) per operation |

## Brain Trigger (One Line)
> Count loops. If nested, check if inner loop is GATED by a condition —
> if gated, ADD instead of MULTIPLY. Watch for hidden O(n) inside one-liners.
```

That's your full sheet — covers 95% of what you'll ever need to eyeball complexity fast. 
