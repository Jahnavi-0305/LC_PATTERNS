# P9 — Variable Sliding Window

## When to Use
- "longest subarray/substring with..."
- "minimum size subarray with sum ≥ K"
- "at most K distinct characters / replacements"
- "no repeating characters"

## Trigger Clue Words
longest | shortest | minimum size | at most K | no repeating | subarray/substring + condition

## The Non-Negotiable Rules
1. left = 0 (always hardcoded before loop)
2. right = 0 (range() handles it, never set manually)
3. right NEVER goes back
4. left ONLY moves inside the while loop
5. window size = right - left + 1 (always)

## Master Template
```python
def variable_sliding_window(arr):
    left = 0
    window_state = 0      # use int for sum, set/dict for characters
    result = 0            # use float('inf') if finding MINIMUM

    for right in range(len(arr)):
        # STEP 1: Expand — add right element to window
        window_state += arr[right]

        # STEP 2: Shrink — fix window if rule is broken
        while window_state > CONDITION:
            window_state -= arr[left]
            left += 1

        # STEP 3: Update result — window is valid here
        result = max(result, right - left + 1)

    return result
```

## Variants

### Longest → maximize window
```python
result = 0
# update inside for loop after while
result = max(result, right - left + 1)
```

### Shortest → minimize window
```python
result = float('inf')
# update INSIDE while loop, when condition is met
while window_state >= target:
    result = min(result, right - left + 1)
    window_state -= arr[left]
    left += 1
return result if result != float('inf') else 0
```

### Has Characters → use set or dict
```python
window_state = {}   # or set()

# add
window_state[arr[right]] = window_state.get(arr[right], 0) + 1

# remove
window_state[arr[left]] -= 1
if window_state[arr[left]] == 0:
    del window_state[arr[left]]
left += 1
```

## The 3 Problems to Remember
| Problem | Type | Condition |
|---|---|---|
| Longest Subarray with Sum ≤ K | Longest | shrink when sum > K |
| Longest Substring Without Repeating Chars | Longest + chars | shrink when duplicate found |
| Minimum Size Subarray Sum ≥ Target | Shortest | shrink when sum >= target |

## Brain Trigger (One Line Summary)
> right expands always, left shrinks only when window breaks, result updates when window is valid.


95% of all sliding window problems cover these situations: 

| Situation                            | Use This            |
| ------------------------------------ | ------------------- |
| Sum / total of numbers               | int                 |
| Count of characters / frequency      | dict                |
| Just checking if char exists or not  | set                 |
| Track index/position of a character  | dict (char → index) |
| Count of distinct elements in window | int counter + dict  |
