# Sliding Window Patterns That Solve Most Subarray & Substring Problems

## When Should Sliding Window Click in Your Mind?

Think Sliding Window when you see:

- A **contiguous subarray or substring** is involved (not any subset — must be contiguous)
- Keywords like "longest", "shortest", "maximum", "minimum", "at most", "at least", "exactly k"
- A **fixed-size window** (e.g., "subarray of size k")
- You need to track counts/frequency of characters within a moving range
- The brute force checks every subarray/substring with nested loops (O(n²) or worse)

If the brute force looks like:

```python
for i in range(n):
    for j in range(i, n):
        # process subarray from i to j
        ...
```

...and you notice you're **re-processing overlapping parts** of the array every time `i` moves, 👉 **Sliding Window is the correct approach.**

---

## Pattern 8: Fixed Size Window

**When to use:**
- The problem gives you an exact window size `k`
- Common phrasing: "subarray of size k", "every k consecutive elements"

**Key idea:**
Build the first window of size `k`. Then slide it one step at a time: **add** the new element entering the window on the right, **remove** the old element leaving the window on the left. This avoids recomputing the whole window from scratch every time.

**Template:**
```python
window_sum = sum(nums[:k])
best = window_sum

for i in range(k, len(nums)):
    window_sum += nums[i]        # add the new element
    window_sum -= nums[i - k]    # remove the element leaving the window
    best = max(best, window_sum)

return best
```

**Why it works:** Since the window size never changes, each slide only does O(1) work (one addition, one subtraction) instead of re-summing the whole window — turning an O(n·k) brute force into O(n).

**Problems:**
- [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/)
- [Maximum Sum of Distinct Subarrays With Length K](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/)
- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) *(also uses Pattern 10)*

---

## Pattern 9: Variable Size Window

**When to use:**
- The window size **isn't fixed** — it grows and shrinks based on a condition
- Common phrasing: "longest subarray such that...", "shortest subarray such that...", "at most k distinct...", "sum ≤ target"

**Key idea:**
Keep expanding the window by moving `right` forward. Once the window **breaks the condition** (e.g., sum exceeds target, too many distinct characters), shrink it by moving `left` forward until the condition is valid again. Track the best window size along the way.

**Template:**
```python
left = 0
window_state = {}   # or a running sum/count, depending on the problem
best = 0

for right in range(len(nums)):
    # 1. add nums[right] into the window
    update_window(window_state, nums[right])

    # 2. shrink from the left while the window is invalid
    while not is_valid(window_state):
        remove_from_window(window_state, nums[left])
        left += 1

    # 3. window [left, right] is now valid — update the answer
    best = max(best, right - left + 1)

return best
```

**Why it works:** `right` only ever moves forward, and `left` only ever moves forward too — neither pointer ever goes backward. So even though it looks like nested loops, each pointer sweeps the array at most once, giving O(n) total instead of O(n²).

**Problems:**
- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
- [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
- [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
- [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

---

## Pattern 10: Monotonic Queue for Max/Min

**When to use:**
- You need the **maximum or minimum** value inside a sliding window, and the window moves across the whole array
- A plain sliding window would need O(k) time per window just to find the max/min — this pattern gets it down to O(1) amortized

**Key idea:**
Maintain a **deque** (double-ended queue) that stores useful *indices*, always kept in decreasing order of value (for max) from front to back.
- Before adding a new index, **pop from the back** any indices whose values are smaller than the new value — they can never be the max while the new element is still in the window.
- **Pop from the front** any index that has fallen outside the window.
- The front of the deque is always the max of the current window.

**Template:**
```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()  # stores indices, values in decreasing order
    result = []

    for i in range(len(nums)):
        # remove indices out of the window
        while dq and dq[0] <= i - k:
            dq.popleft()

        # remove smaller values from the back — they're useless now
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()

        dq.append(i)

        if i >= k - 1:
            result.append(nums[dq[0]])  # front is the current max

    return result
```

**Why it works:** Every index is added to the deque exactly once and removed at most once, so the total work across the whole array is O(n) — even though it looks like there's a queue operation happening per window.

**Problems:**
- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
- [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/)

---

## Pattern 11: Character Frequency Matching

**When to use:**
- You need to find substrings that are **anagrams**, **permutations**, or contain **all characters of another string**
- Common phrasing: "find all anagrams", "smallest substring containing all characters of t", "permutation in string"

**Key idea:**
Keep a frequency count (hash map or fixed-size array for 26 lowercase letters) of what you **need**, and a running frequency count of what's **currently in your window**. Track how many required characters currently have their count fully satisfied (`matched`). Expand the window on the right, and shrink from the left whenever it's valid or over-satisfied.

**Template:**
```python
from collections import Counter

def min_window(s, t):
    need = Counter(t)
    window = {}
    have, required = 0, len(need)

    left = 0
    best_len, best_left = float('inf'), 0

    for right in range(len(s)):
        ch = s[right]
        window[ch] = window.get(ch, 0) + 1

        if ch in need and window[ch] == need[ch]:
            have += 1

        # shrink while the window fully satisfies the requirement
        while have == required:
            if right - left + 1 < best_len:
                best_len = right - left + 1
                best_left = left

            left_ch = s[left]
            window[left_ch] -= 1
            if left_ch in need and window[left_ch] < need[left_ch]:
                have -= 1
            left += 1

    return "" if best_len == float('inf') else s[best_left:best_left + best_len]
```

**Why it works:** Instead of comparing full frequency maps on every window (which is expensive), you track a single counter (`have` vs `required`) that only changes when a character's count crosses a threshold — making each step O(1) instead of O(26) or O(n).

**Problems:**
- [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- [Permutation in String](https://leetcode.com/problems/permutation-in-string/)

---

## Common Mistakes to Avoid

❌ Using sliding window on a problem that asks for **any subset**, not a **contiguous** subarray/substring
✅ Sliding window only works when the elements must stay in order and touching — otherwise think subsets/backtracking

❌ In Variable Size Window, using an `if` instead of a `while` to shrink the window
✅ Always use `while not is_valid(...)` — one shrink step might not be enough to fix the window

❌ In Fixed Size Window, forgetting to initialize the **first window** before starting the loop
✅ Compute the sum/state of `nums[0:k]` first, then start sliding from index `k`

❌ In Monotonic Queue, popping from the back based on **index** instead of **value**
✅ Pop from the back while `nums[dq[-1]] < nums[i]` (compare values, not positions)

❌ In Character Frequency Matching, comparing entire frequency dictionaries every iteration
✅ Track a single `have`/`required` counter instead — much faster and simpler

---

## Must-Solve Sliding Window Questions (In Order)

**Easy**
- [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/)

**Medium**
- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
- [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
- [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
- [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- [Permutation in String](https://leetcode.com/problems/permutation-in-string/)

**Hard**
- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
- [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)

---

## Final Tip (Interview Gold)

If you can answer:

**"Does the window only need to grow and shrink from the edges, without ever having to look back inside it?"**

👉 Sliding Window is the correct solution.

Happy Coding 🚀
