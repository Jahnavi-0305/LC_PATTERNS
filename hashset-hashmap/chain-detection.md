# HashSet Chain Detection

## When to Use
- "longest consecutive sequence" (numbers, order doesn't matter)
- Shuffling the array wouldn't change the answer
- Word "consecutive" appears, NOT "subsequence"

## Trigger Test
Shuffle the array. Does the answer change?
  NO  → this pattern
  YES → probably DP instead

## Locked Template
```python
def longest_consecutive(nums):
    num_set = set(nums)
    longest = 0
    for num in num_set:
        if (num - 1) not in num_set:      # confirms true chain start
            length = 1
            while (num + length) in num_set:
                length += 1
            longest = max(longest, length)
    return longest
```

## Why O(n) Not O(n²)
The "if (num-1) not in num_set" gate means the while loop only
runs for TRUE starting numbers. Total while-loop work across
the whole run sums to O(n).

## Sibling Problems
- 128. Longest Consecutive Sequence
- 1218. Longest Arithmetic Subsequence of Given Difference (step = difference)
- 298. Binary Tree Longest Consecutive Sequence (same logic, tree-shaped)

## Brain Trigger
> Set for O(1) lookup. Only start counting from true chain starts.
> Walk forward, count, track max.
