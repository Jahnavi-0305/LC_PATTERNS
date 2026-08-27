## Kadane's-style template ("running best, extend-or-restart")

**Core recognition trigger:**
*"I need the best contiguous run (max/min sum, max/min product, longest/shortest streak) in a single array, and at each position I only need to know one thing about the past: was continuing worth it, or should I restart from here?"*

**The template:**
```
best_overall = arr[0]
best_ending_here = arr[0]

for i in range(1, len(arr)):
    best_ending_here = combine(arr[i], best_ending_here)   # extend or restart
    best_overall = compare(best_overall, best_ending_here)  # update global best

return best_overall
```

The two lines you fill in per-problem are `combine` and `compare`. Everything else stays the same. That's the whole master template — one running "best if I include current position," one running "best ever seen."

### How to fill it in per problem type:

**Max subarray sum (classic Kadane's):**
- `combine`: `max(arr[i], best_ending_here + arr[i])` — extend if positive, restart if the running sum turned negative
- `compare`: `max(best_overall, best_ending_here)`

**Max product subarray (twist: negatives flip sign):**
- Need to track running **min** too, because a negative number can turn a running min into the new max
- `combine`: `max_end = max(arr[i], max_end*arr[i], min_end*arr[i])`, `min_end = min(arr[i], max_end_prev*arr[i], min_end*arr[i])`
- This is the same skeleton, just two running values instead of one — the pattern didn't change, the state you're tracking got richer

**Best Time to Buy/Sell Stock (running min, not Kadane's directly, but same family):**
- `combine`: track `min_price = min(min_price, price)` instead of extend/restart
- `compare`: `max_profit = max(max_profit, price - min_price)`

**Longest streak of something (e.g., longest increasing run):**
- `combine`: `current_len = current_len + 1 if arr[i] > arr[i-1] else 1` — extend or reset to 1
- `compare`: `best_len = max(best_len, current_len)`

### The one question that tells you this is the pattern:
**"Can I describe the answer at position `i` using only the answer at position `i-1`, without needing to look further back or revisit past decisions?"**

If yes → this template. If you need to look back arbitrarily far, or revisit/undo a choice → not this pattern, probably real DP with an array of states, or a stack.

That's the full reusable skeleton — two variables, one loop, two lines that change per problem.

**Best Time to Buy/Sell Stock is genuinely DP** — not "kind of like DP," not "a bridge to DP." Here's the honest formal version:

Define `dp[i] = min price seen from day 0 to day i`. Then the answer at each day is `prices[i] - dp[i-1]`, and you take the max over all days. That min-price-so-far *is* the DP state — it's exactly "today's answer depends on yesterday's state," which is your own definition of DP from earlier in this conversation. So I wasn't wrong to call it DP — where I was sloppy was calling the Kadane's template a "seed" or "bridge" into DP, like it's a separate, lesser thing that leads up to real DP. That's misleading. It isn't a stepping stone to DP. **It is DP** — specifically, 1D DP where you only need the immediately previous state, so you collapse the DP array into a single running variable instead of keeping a full `dp[]` array.

That's the actual precise statement: **Kadane's-style "running best" problems are DP with O(1) space instead of O(n) space**, because the recurrence only reaches back one step. That's not a different pattern loosely related to DP — it's the exact same pattern, just with the optimization already applied (since you never need `dp[i-2]` or earlier, no need to store the whole array).

So to directly answer your question: I shouldn't have framed it as "bridges you into" — it's already there, fully DP, and calling it a bridge undersold what you'd already correctly identified. Good catch.
