# Two Pointer Patterns That Solve Most Array & String Problems

## When Should Two Pointers Click in Your Mind?

Think Two Pointers when you see:

- A **sorted array** and you need a pair/triplet with some sum condition
- You need to check something **from both ends** (palindrome, reverse, etc.)
- You need to detect a **cycle** or find a **middle point**
- You need to compare/build a string while **skipping certain characters**
- You need to do something **in-place** without extra space
- The brute force is O(n²) with nested loops, and you notice one pointer never needs to go backward

If the brute force looks like:

```python
for i in range(n):
    for j in range(n):
        ...
```

...and one of those loops is redundant because of sorted order or a symmetric structure, 👉 **Two Pointers is the correct approach.**

---

## Pattern 1: Converging (Opposite Direction) Pointers

**When to use:**
- Array is sorted
- You need a pair (or triplet) that satisfies a sum/comparison condition
- You'd otherwise check every pair with nested loops

**Key idea:**
Start one pointer at the beginning (`left`) and one at the end (`right`). Move them toward each other based on a comparison:
- If the current combination is **too small**, move `left` forward (increase value)
- If the current combination is **too big**, move `right` backward (decrease value)
- If it matches, you found your answer

**Template:**
```python
left, right = 0, len(nums) - 1

while left < right:
    total = nums[left] + nums[right]
    if total == target:
        # found the pair
        break
    elif total < target:
        left += 1   # need a bigger sum
    else:
        right -= 1  # need a smaller sum
```

**Why it works:** Because the array is sorted, moving `left` only ever increases the sum, and moving `right` only ever decreases it. You never need to re-check a combination twice.

**Problems:**
- [Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
- [3Sum](https://leetcode.com/problems/3sum/)
- [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
- [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

---

## Pattern 2: Fast & Slow Pointers

**When to use:**
- Linked list problems (cycle detection, finding the middle, finding a duplicate)
- You need to find a point that's some fixed relationship away from the end (e.g., "the middle")

**Key idea:**
Two pointers start at the same place. `slow` moves 1 step at a time, `fast` moves 2 steps at a time.
- If there's a **cycle**, `fast` will eventually lap `slow` and they'll meet.
- If there's **no cycle**, when `slow` reaches the middle, `fast` has already reached the end (because it moves twice as fast).

**Template:**
```python
slow, fast = head, head

while fast is not None and fast.next is not None:
    slow = slow.next
    fast = fast.next.next
    if slow == fast:
        # cycle detected
        break
# if no cycle: slow is now at the middle node
```

**Why it works:** Think of it like two runners on a circular track — the faster one always catches up to the slower one eventually if there's a loop. If there's no loop, the fast runner simply finishes first.

**Problems:**
- [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
- [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
- [Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)
- [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)
- [Happy Number](https://leetcode.com/problems/happy-number/)

---

## Pattern 3: Fixed Separation (Sliding Gap)

**When to use:**
- You need two elements that are always a **fixed distance `k` apart**
- Often used to detect duplicates within a window, or "Nth node from the end" type problems

**Key idea:**
Move `right` pointer `k` steps ahead of `left` first. Then move both pointers together, one step at a time, keeping the gap constant.

**Template:**
```python
left, right = 0, k

# right starts k steps ahead of left (setup phase)

while right < len(nums):
    # compare/process nums[left] and nums[right]
    left += 1
    right += 1
```

**Why it works:** By locking the gap between the two pointers at `k`, every step naturally compares elements that are exactly `k` apart — no need to recalculate the gap each time.

**Problems:**
- [Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/)
- [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

---

## Pattern 4: In-place Array Modification

**When to use:**
- You need to remove/overwrite elements in an array **without using extra space**
- Common phrasing: "modify the array in-place", "return the new length"

**Key idea:**
Use one pointer (`slow` or `writeIndex`) to track **where the next valid element should go**, and another pointer (`fast` or `readIndex`) to **scan through the array**. When `fast` finds a valid element, write it at `slow`'s position and advance `slow`.

**Template:**
```python
slow = 0

for fast in range(len(nums)):
    if is_valid(nums[fast]):
        nums[slow] = nums[fast]
        slow += 1
# slow is now the new length of the valid portion
```

**Why it works:** `slow` never moves faster than `fast`, so you're always writing into a position you've already read past — you can never overwrite data you still need.

**Problems:**
- [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
- [Remove Element](https://leetcode.com/problems/remove-element/)
- [Move Zeroes](https://leetcode.com/problems/move-zeroes/)
- [Sort Colors](https://leetcode.com/problems/sort-colors/)

---

## Pattern 5: String Comparison With Special Characters

**When to use:**
- Comparing two strings while **ignoring/skipping** certain characters (e.g., backspaces, punctuation, spaces)

**Key idea:**
Walk two pointers from the **end** of each string backward (since operations like backspace affect what comes before them). At each step, skip over characters that should be ignored before comparing.

**Template:**
```python
i, j = len(s) - 1, len(t) - 1

while i >= 0 or j >= 0:
    i = skip_invalid(s, i)   # move i past characters to ignore
    j = skip_invalid(t, j)   # move j past characters to ignore

    if i >= 0 and j >= 0:
        if s[i] != t[j]:
            return False
    elif i >= 0 or j >= 0:
        return False  # one string still has leftover valid characters

    i -= 1
    j -= 1

return True
```

**Why it works:** Processing from the end lets you resolve "delete" style operations correctly, because a backspace always cancels the character immediately before it — which you only know once you're scanning backward.

**Problems:**
- [Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)
- [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
- [Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii/)

---

## Pattern 6: Expanding From Center

**When to use:**
- Finding palindromic substrings
- The problem involves checking symmetry **around every possible center**

**Key idea:**
For every index (and every gap between two indices, for even-length cases), treat it as a potential center. Expand `left` and `right` outward one step at a time as long as the characters match.

**Template:**
```python
def expand_from_center(s, left, right):
    while left >= 0 and right < len(s) and s[left] == s[right]:
        left -= 1
        right += 1
    return left + 1, right - 1  # valid palindrome bounds

for i in range(len(s)):
    expand_from_center(s, i, i)       # odd-length palindrome, center = i
    expand_from_center(s, i, i + 1)   # even-length palindrome, center = between i and i+1
```

**Why it works:** Every palindrome has a center (a single character or a gap between two characters). By trying every possible center and expanding outward, you cover every possible palindrome in the string.

**Problems:**
- [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

---

## Pattern 7: String/Array Reversal

**When to use:**
- You need to reverse a string, array, or part of one — usually **in-place**

**Key idea:**
Start `left` at the beginning, `right` at the end. Swap them, then move `left` forward and `right` backward until they meet in the middle.

**Template:**
```python
left, right = 0, len(arr) - 1

while left < right:
    arr[left], arr[right] = arr[right], arr[left]
    left += 1
    right -= 1
```

**Why it works:** Every swap correctly places one element from each end into its final mirrored position, and since `left` and `right` move toward each other, every element gets visited exactly once.

**Problems:**
- [Reverse String](https://leetcode.com/problems/reverse-string/)
- [Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)
- [Reverse Vowels of a String](https://leetcode.com/problems/reverse-vowels-of-a-string/)

---

## Common Mistakes to Avoid

❌ Using two pointers on an **unsorted** array when the pattern requires sorted order (Pattern 1)
✅ Sort first if the problem doesn't guarantee sorted input

❌ Forgetting the `fast.next is not None` check in Fast & Slow (causes crash on odd-length lists)
✅ Always check both `fast is not None` and `fast.next is not None`

❌ In in-place modification, writing to `slow` before confirming `fast`'s element is valid
✅ Only advance `slow` and write **after** validating the condition

❌ In "Expanding From Center", forgetting to check the **even-length** case (center between two indices)
✅ Always run both `expand_from_center(i, i)` and `expand_from_center(i, i+1)`

❌ Infinite loops from forgetting to move at least one pointer every iteration
✅ Double check every branch of your `while` loop moves `left` and/or `right`

---

## Must-Solve Two Pointer Questions (In Order)

**Easy**
- [Reverse String](https://leetcode.com/problems/reverse-string/)
- [Move Zeroes](https://leetcode.com/problems/move-zeroes/)
- [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
- [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

**Medium**
- [Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
- [3Sum](https://leetcode.com/problems/3sum/)
- [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
- [Sort Colors](https://leetcode.com/problems/sort-colors/)
- [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

**Hard**
- [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

---

## Final Tip (Interview Gold)

If you can answer:

**"Can I solve this with two indices moving toward each other, away from each other, or at different speeds — instead of nested loops?"**

👉 Two Pointers is the correct solution.

Happy Coding 🚀
