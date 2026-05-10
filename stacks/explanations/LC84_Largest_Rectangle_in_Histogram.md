# LC 84 — Largest Rectangle in Histogram

## Problem Summary

Given an array `heights` where `heights[i]` is the height of the i-th bar in a histogram (all bars have width 1), find the area of the **largest rectangle** that fits entirely within the histogram.

**Example:**
```
Input:  heights = [2, 1, 5, 6, 2, 3]
Output: 10   (rectangle of height 5, width 2, covering bars at index 2–3)
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to find the largest axis-aligned rectangle that fits within the bars of a histogram — the rectangle can span multiple bars but is limited in height by the shortest bar in that span."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Can heights be 0? That would create a natural split in the histogram."
- "Can heights be negative? I'll assume no — they represent bar heights."
- "Is the array guaranteed non-empty?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force is O(n²): for every pair of indices (i, j), find the minimum height in that range and compute width × min_height. We can beat this with a monotonic stack that detects in O(1) when a rectangle's right boundary is found."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is a **monotonic increasing stack** that stores `(start_index, height)` pairs. When we encounter a bar shorter than the stack top, the top's rectangle is 'trapped' — it cannot extend further right. We pop it, compute its area using the current index as its right boundary, and merge its start index leftward. After the main loop, flush remaining stack items against the right wall."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space — each bar is pushed and popped at most once."

### Step 6 — Code with Narration (~5–8 minutes)
> "Stack stores tuples of (start, height) — start is the leftmost index this height can extend to..."
> "For each bar, I initialize start = i. While the stack top has height ≥ current h, I pop it. Its area = height × (i - start_index). I set start = that popped start, because the current bar can actually extend back to where the popped bar started..."
> "After the loop, remaining stack items haven't been closed — their right boundary is the end of the array, so width = n - j..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "heights=[2,1,5,6,2,3]. Push (0,2). i=1,h=1: pop (0,2), area=2×(1-0)=2, start=0. Push (0,1). i=2,h=5: push (2,5). i=3,h=6: push (3,6). i=4,h=2: pop (3,6) area=6×(4-3)=6, pop (2,5) area=5×(4-2)=10 ← max, start=2. Push (2,2). i=5,h=3: push (5,3). Flush: (5,3)→3×1=3, (2,2)→2×4=8, (0,1)→1×6=6. Max = 10."

### Step 8 — Handle Edge Cases (~1 minute)
> "A single bar: stack gets one entry, flushed at the end — area = heights[0] × 1. All bars same height: they all stay in the stack until the flush. Strictly decreasing heights: every new bar pops all previous entries."

---

## Mental Model / Intuition

A rectangle's area is bounded by its **shortest bar**. The question is: for each bar, what is the widest rectangle where that bar is the shortest?

The answer is: extend left until you hit a shorter bar, and extend right until you hit a shorter bar. The monotonic stack does exactly this — entries stay in the stack while they are potential minimums of their range. When a shorter bar arrives and pops an entry, that popped entry's left and right boundaries are both now known. Area can be computed immediately.

The **start index inheritance** is the subtle trick: when bar A at index 3 gets popped by a shorter bar at index 4, the current bar at index 4 can actually extend left to wherever bar A started — because everything between bar A's start and index 4 was already popped (and computed) and was taller than bar A.

---

## Approach

1. Initialize `stack = []` (stores `(start, height)`) and `max_area = 0`.
2. For each `(i, h)` in `enumerate(heights)`:
   - Set `start = i`
   - While stack is non-empty and `stack[-1][1] >= h`:
     - Pop `(j, height)`, compute `area = height × (i - j)`, update `max_area`
     - Set `start = j` (inherit the popped bar's left boundary)
   - Push `(start, h)`
3. Flush remaining stack: for each `(j, height)`, compute `area = height × (n - j)`.
4. Return `max_area`.

---

## Code

```python
def largestRectangleArea(heights):
    stack = []        # (start, height)
    max_area = 0

    for i, h in enumerate(heights):
        start = i
        while stack and stack[-1][1] >= h:
            j, height = stack.pop()
            max_area = max(max_area, height * (i - j))
            start = j
        stack.append((start, h))

    for j, height in stack:
        max_area = max(max_area, height * (len(heights) - j))

    return max_area
```

---

## Step-by-Step Walkthrough

Input: `[2, 1, 5, 6, 2, 3]`

| i | h | Action | Stack | max_area |
|---|---|--------|-------|----------|
| 0 | 2 | push | [(0,2)] | 0 |
| 1 | 1 | pop(0,2)→area=2, push(0,1) | [(0,1)] | 2 |
| 2 | 5 | push | [(0,1),(2,5)] | 2 |
| 3 | 6 | push | [(0,1),(2,5),(3,6)] | 2 |
| 4 | 2 | pop(3,6)→6, pop(2,5)→**10**, push(2,2) | [(0,1),(2,2)] | **10** |
| 5 | 3 | push | [(0,1),(2,2),(5,3)] | 10 |
| flush | — | (5,3)→3, (2,2)→8, (0,1)→6 | [] | 10 |

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Each bar pushed and popped at most once |
| **Space** | O(n) | Stack holds at most n entries |

---

## Comparison Table

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute force (all pairs) | O(n²) | O(1) | Too slow for large inputs |
| Divide & conquer | O(n log n) avg | O(log n) | Cleaner to reason about, slower |
| Monotonic stack | O(n) | O(n) | Optimal |

---

## Follow-Up Questions

**Q: What does the stack represent at any moment?**
It's all the "open" rectangles — bars whose left boundary is known but whose right boundary hasn't been found yet. The stack is monotonically increasing in height, so the top is always the "most recently opened" rectangle.

**Q: Why do we use `>=` not `>` in the while condition?**
When equal heights are adjacent, we want to merge them into one wider rectangle rather than computing duplicate areas. Using `>=` pops the earlier one and lets the later one inherit its start index.

**Q: What's the largest rectangle in a histogram of all zeros?**
Area = 0. The while loop never pops anything (all heights are 0 = h), and the flush computes 0 × n = 0.
