# LC 739 — Daily Temperatures

## Problem Summary

Given an array `temperatures`, return an array `answer` where `answer[i]` is the number of days you have to wait after the i-th day to get a warmer temperature. If no warmer day exists, `answer[i] = 0`.

**Example:**
```
Input:  temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to find, for each day, how many days until the next day that is strictly warmer. If no such day exists, the answer is 0."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Are temperatures guaranteed to be positive integers?"
- "What's the range of temperatures? This affects whether a bucket sort could help."
- "Is it strictly warmer, or warmer-than-or-equal-to? Based on the problem, I'll assume strictly greater."

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force is O(n²): for each day i, scan forward until we find a warmer day. For large inputs that's too slow. With a monotonic stack, we can do O(n) by keeping track of days that are still 'waiting' for their answer."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is a **monotonic decreasing stack of indices**. As I scan left to right, I push each index. When I find a temperature warmer than the stack top, I pop that index and calculate the wait: `current_index - popped_index`. The stack always stays in decreasing order of temperature — each entry is an unresolved day waiting for a warmer future day."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space — each index is pushed and popped at most once."

### Step 6 — Code with Narration (~5–8 minutes)
> "I initialize answer as all zeros and an empty stack..."
> "For each index i with temperature t, while the stack is non-empty and the top's temperature is less than t, I pop that index j, set answer[j] = i - j, and continue..."
> "After the while loop, I push the current index onto the stack since it hasn't found a warmer day yet..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "temps=[73,74,...]. i=0,t=73: stack empty, push 0. i=1,t=74: 74>temps[0]=73, pop 0 → answer[0]=1-0=1. Push 1. i=2,t=75: 75>74, pop 1 → answer[1]=2-1=1. Push 2. i=3,t=71: 71<75, just push 3. i=4,t=69: 69<71, push 4. i=5,t=72: 72>69, pop 4 → answer[4]=5-4=1; 72>71, pop 3 → answer[3]=5-3=2; 72<75, push 5. Continue..."

### Step 8 — Handle Edge Cases (~1 minute)
> "If temperatures is strictly decreasing (like [5,4,3,2,1]), the stack keeps growing and is never popped. All answer values stay 0 — which is correct since no warmer day ever comes."

---

## Mental Model / Intuition

Imagine each day as a person standing in line, waiting to see a warmer day. Each person holds their temperature. When a new (warmer) person arrives, they can signal everyone behind them who is colder: "Your wait is over — I'm the warmer day you've been waiting for."

The stack is that waiting line. Because the stack stays in **decreasing temperature order**, a single warmer arrival can resolve multiple days at once. The most recent (coldest) days are resolved first — LIFO. This is what gives us O(n) amortized: each day is added and removed exactly once.

---

## Approach

1. Initialize `answer = [0] * n` and `stack = []` (stores indices).
2. For each `(i, t)` in `enumerate(temperatures)`:
   - While stack is non-empty and `temperatures[stack[-1]] < t`:
     - Pop `j`; set `answer[j] = i - j`
   - Push `i`
3. Return `answer` (unpopped indices remain 0 by default).

---

## Code

```python
def dailyTemperatures(self, temperatures):
    n = len(temperatures)
    answer = [0] * n
    stack = []  # [index]
    for i, t in enumerate(temperatures):
        while stack and temperatures[stack[-1]] < t:
            j = stack.pop()
            answer[j] = i - j
        stack.append(i)
    return answer
```

---

## Step-by-Step Walkthrough

Input: `[73, 74, 75, 71, 69, 72, 76, 73]`

| i | t | Stack before | Action | answer |
|---|---|--------------|--------|--------|
| 0 | 73 | [] | push 0 | [0,0,0,0,0,0,0,0] |
| 1 | 74 | [0] | pop 0 → ans[0]=1, push 1 | [1,0,0,0,0,0,0,0] |
| 2 | 75 | [1] | pop 1 → ans[1]=1, push 2 | [1,1,0,0,0,0,0,0] |
| 3 | 71 | [2] | push 3 | [1,1,0,0,0,0,0,0] |
| 4 | 69 | [2,3] | push 4 | [1,1,0,0,0,0,0,0] |
| 5 | 72 | [2,3,4] | pop 4→ans[4]=1, pop 3→ans[3]=2, push 5 | [1,1,0,2,1,0,0,0] |
| 6 | 76 | [2,5] | pop 5→ans[5]=1, pop 2→ans[2]=4, push 6 | [1,1,4,2,1,1,0,0] |
| 7 | 73 | [6] | push 7 | [1,1,4,2,1,1,0,0] |

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Each index pushed and popped at most once |
| **Space** | O(n) | Stack holds at most n indices |

---

## Follow-Up Questions

**Q: Why does the stack store indices instead of temperatures?**
We need the index to calculate the wait time (`i - j`) and to write back into `answer[j]`. Temperatures alone are not enough.

**Q: What if we need the next greater element, not the number of days to wait?**
Same algorithm — instead of computing `i - j`, store `temperatures[i]` as the answer for index j. This is the classic "Next Greater Element" pattern.

**Q: Why is the stack monotonically decreasing in temperature?**
Because once we push an index, we only pop it when something warmer arrives. So any index that remains in the stack was never warmer than what came after it — meaning everything in the stack is in decreasing order of temperature by definition.
