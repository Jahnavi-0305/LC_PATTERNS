***

## Starting Positions Only

| Pattern | Left Starts At | Right Starts At |
|---|---|---|
| Converging | `0` | `len(arr) - 1` |
| Expand From Center | `i` (current index) | `i` (same index, for odd) OR `i+1` (for even) |
| String Reversal | `0` | `len(arr) - 1` |
| String Comparison (backspace) | `len(s) - 1` | `len(t) - 1` |
| In-place Modification | `0` (called `write`) | `0` (called `read`, not really "right") |

***

## Visual Cheat Sheet

```
CONVERGING:
[2, 7, 11, 15]
 L           R          ← L=0, R=len-1 (opposite ends)

EXPAND FROM CENTER:
"babad"
    i                    ← for ODD: L=i, R=i (same spot, start together)
    i i+1                ← for EVEN: L=i, R=i+1 (next to each other)

STRING REVERSAL:
"hello"
 L     R                 ← L=0, R=len-1 (same as converging)

STRING COMPARISON (backspace):
s="ab#c"          t="ad#c"
        i                       j
      (len(s)-1)              (len(t)-1)   ← BOTH start from the END, not beginning

IN-PLACE MODIFICATION:
[0,0,1,1,1,2,2]
 write,read              ← both start at 0, but they're not "opposite ends"
                            they move forward TOGETHER, write only jumps when needed
```

***

## The One-Line Memory Trigger for Each

```
Converging        → L=0, R=end        (opposite ends, walk in)
Expand from Center → L=R=i            (same spot, walk out)
String Reversal    → L=0, R=end        (same as converging)
String Comparison  → L=end, R=end      (both start from the back)
In-place Mod       → write=0, read=0   (both start front, move forward together)
```

That's all 5 starting setups, locked.
