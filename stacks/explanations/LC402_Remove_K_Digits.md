# LC 402 — Remove K Digits

## Problem Summary

Given a string `num` representing a non-negative integer and an integer `k`, remove `k` digits from `num` to produce the **smallest possible number**. Return the result as a string with no leading zeros.

**Example:**
```
Input:  num = "1432219", k = 3
Output: "1219"
Explanation: Remove 4, 3, 2 (or 4, 3, 2 in a greedy left-to-right pass) → "1219"
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to remove exactly k digits from a number string to produce the smallest possible result, and return it with no leading zeros."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Can `num` have leading zeros in the input, or is it guaranteed to be a valid non-negative integer?"
- "What if `k` equals the length of `num`? Presumably the result is '0'."
- "Is the input guaranteed to be non-empty?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to try all C(n, k) combinations of which digits to keep, convert each to an integer, and return the minimum — that's exponential. The key greedy insight: to minimize a number, make the leftmost digits as small as possible. If a digit is larger than the digit after it, removing it makes the number smaller."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is a **monotonic increasing stack**. I scan left to right. Whenever the current digit is smaller than the stack top AND I still have removals left, I pop the top (remove that larger digit) and decrement k. After the loop, if k is still positive, trim the last k digits (they're at the end of the increasing stack — the largest). Then strip leading zeros."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space — each digit is pushed and popped at most once."

### Step 6 — Code with Narration (~5–8 minutes)
> "I scan each digit d in the number string..."
> "While k > 0, the stack is non-empty, and the stack top is greater than d: pop the top and decrement k. This removes a larger digit that sits left of a smaller one..."
> "Push d onto the stack..."
> "After the loop, if k still > 0, I trim the last k digits with stack[:-k]..."
> "Join, strip leading zeros, return '0' if empty string..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "num='1432219', k=3. d=1: push. d=4: 4>1? No, push. d=3: 3<4 and k=3>0 → pop 4, k=2. d=3: 3>1 but not <1, push. d=2: 2<3 → pop 3, k=1. d=2: 2>1, push. d=1: 1<2 → pop 2, k=0. Push 1. d=9: push. Stack=[1,2,1,9]. k=0, no trim. ''.join='1219'. No leading zeros. Result='1219'."

### Step 8 — Handle Edge Cases (~1 minute)
> "k = len(num): all digits removed → stack becomes empty → return '0'. num='10200', k=1: pop 1 (1>0)? No: 1<0? No — wait, '1' vs '0': 1>0, pop 1, push 0, push 2, push 0, push 0. Stack=[0,2,0,0], trim 0, join='0200', lstrip='200'. Hmm — let me reconsider: with k=1, we get '0200'.lstrip('0')='200'. Correct."

---

## Mental Model / Intuition

Think of the number digit by digit from left to right. You want each position to be as small as possible.

When you see a digit that is **smaller than the one before it**, the one before it is a waste — it made the number bigger. Remove it. You "bought" a smaller number by spending one of your k removals. Repeat until the new digit is not smaller than the top, or k runs out.

The stack maintains a **non-decreasing sequence of digits** — the greedily minimal number built so far. If k isn't fully spent after the main loop, the leftover removals come off the end (since the stack is non-decreasing, the rightmost digits are the largest and thus most worth removing last).

---

## Approach

1. Initialize `stack = []`.
2. For each digit `d` in `num`:
   - While `k > 0` and stack is non-empty and `stack[-1] > d`:
     - `stack.pop()`, `k -= 1`
   - `stack.append(d)`
3. If `k > 0`: `stack = stack[:-k]` (trim the largest remaining digits)
4. Return `''.join(stack).lstrip('0') or '0'`

---

## Code

```python
def removeKdigits(self, num, k):
    stack = []
    for d in num:
        while k and stack and stack[-1] > d:
            stack.pop()
            k -= 1
        stack.append(d)
    stack = stack[:-k] if k else stack
    return ''.join(stack).lstrip('0') or '0'
```

---

## Step-by-Step Walkthrough

Input: `num="1432219"`, `k=3`

| digit | stack before | action | k | stack after |
|-------|-------------|--------|---|-------------|
| '1' | [] | push | 3 | ['1'] |
| '4' | ['1'] | push (4>1, no; 4<1? no) | 3 | ['1','4'] |
| '3' | ['1','4'] | pop '4'→k=2, push '3' | 2 | ['1','3'] |
| '2' | ['1','3'] | pop '3'→k=1, push '2' | 1 | ['1','2'] |
| '2' | ['1','2'] | push (2≤2) | 1 | ['1','2','2'] |
| '1' | ['1','2','2'] | pop '2'→k=0, push '1' | 0 | ['1','2','1'] |
| '9' | ['1','2','1'] | push (k=0) | 0 | ['1','2','1','9'] |

k=0, no trim. `''.join(['1','2','1','9'])` = `"1219"`. No leading zeros. **Result: "1219"**

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Each digit pushed and popped at most once |
| **Space** | O(n) | Stack holds at most n digits |

---

## Follow-Up Questions

**Q: Why does the greedy approach work — why not look ahead to pick the best digit to remove?**
Removing a digit that causes a decrease at the earliest position has a guaranteed benefit. The leftmost digit contributes the most to the number's value (highest place value). Greedily removing the first local maximum is always optimal.

**Q: What if the number has leading zeros after removal?**
The `lstrip('0')` handles this. If the result is all zeros (e.g., '0000'), `lstrip` returns `''`, and the `or '0'` guard returns `'0'`.

**Q: What is the condition `stack[:-k] if k else stack` guarding against?**
`stack[:-0]` in Python returns an **empty list** (not the full list). So if `k=0` we must return the full stack unchanged. The `if k else stack` condition prevents this off-by-one bug.
