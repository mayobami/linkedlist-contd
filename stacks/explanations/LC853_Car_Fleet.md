# LC 853 — Car Fleet

## Problem Summary

`n` cars are driving toward a `target` position. Car `i` starts at `position[i]` with speed `speed[i]`. A faster car that catches a slower one forms a **fleet** and moves at the slower car's speed. Return the number of fleets that arrive at the target.

**Example:**
```
Input:  target=12, position=[10,8,0,5,3], speed=[2,4,1,1,3]
Output: 3
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to figure out how many groups of cars arrive at the destination together — faster cars catch slower ones ahead of them and merge into a fleet."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Can two cars start at the same position? I'll assume positions are unique."
- "Do cars that arrive at exactly the same time count as a fleet?"
- "Can a car start at or past the target position?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to simulate movement step by step or find all pairwise catch-up times — O(n²). The key insight is that a car can only be caught by a car behind it, so if I process cars from closest-to-target to farthest, I can immediately see whether each car catches the one ahead."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is to sort cars by position in **descending order** (closest to target first), compute each car's arrival time `(target - pos) / speed`, and use a stack. If the current car's time is greater than the stack top's time, it cannot catch the fleet ahead — it forms a new fleet. If it's equal or less, it merges into the fleet ahead and we don't push."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n log n) time for sorting and O(n) space for the stack."

### Step 6 — Code with Narration (~5–8 minutes)
> "I pair each car's position and speed with zip, then sort descending by position — closest to target first..."
> "For each car I compute arrival time = (target - pos) / speed..."
> "If the stack is empty or this car's time exceeds the top, it can't catch the fleet ahead → push a new fleet time. Otherwise, this car merges — don't push..."
> "The stack length is the number of fleets..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "target=12. Sorted by position: (10,2),(8,4),(5,1),(3,3),(0,1). Times: (12-10)/2=1.0, (12-8)/4=1.0, (12-5)/1=7.0, (12-3)/3=3.0, (12-0)/1=12.0. Stack: push 1.0 (first). 1.0 ≤ 1.0 → merge (no push). 7.0 > 1.0 → push. 3.0 ≤ 7.0 → merge. 12.0 > 7.0 → push. Stack=[1.0, 7.0, 12.0]. Answer=3."

### Step 8 — Handle Edge Cases (~1 minute)
> "All cars have the same speed: they never catch each other, each forms its own fleet. The fastest car is already ahead, so a faster car behind can never overtake a slower one. One car: answer is always 1."

---

## Mental Model / Intuition

Process cars from front to back (closest to target first). Each car is trying to catch the fleet immediately ahead of it.

A car catches the fleet ahead **only if its arrival time ≤ the fleet's arrival time**. If it arrives sooner, it would have had to slow down when it caught up — so it actually arrives together with that fleet. If it arrives later, it can't catch the fleet and forms its own new fleet.

The stack stores the arrival times of all distinct fleets (the "leaders" that nobody caught). Each push = a new fleet that travels independently. Stack length = fleet count.

Why sort descending by position? Because a car can only catch a car that is **ahead of it**. Processing front-to-back means each car's "target" comparison is only with the nearest fleet ahead — the stack top.

---

## Approach

1. Pair positions and speeds: `pairs = sorted(zip(position, speed), reverse=True)`.
2. For each `(pos, spd)` in pairs:
   - Compute `time = (target - pos) / spd`
   - If stack is empty or `time > stack[-1]`: new fleet → push `time`
   - Else: car merges into the fleet ahead → don't push
3. Return `len(stack)`.

---

## Code

```python
def carFleet(self, target, position, speed):
    pairs = sorted(zip(position, speed), reverse=True)
    stack = []
    for pos, spd in pairs:
        time = (target - pos) / spd
        if not stack or time > stack[-1]:
            stack.append(time)
    return len(stack)
```

---

## Step-by-Step Walkthrough

target=12, position=[10,8,0,5,3], speed=[2,4,1,1,3]

After sorting by position descending:

| pos | spd | time | vs stack top | action |
|-----|-----|------|--------------|--------|
| 10 | 2 | 1.0 | stack empty | push 1.0 |
| 8 | 4 | 1.0 | 1.0 ≤ 1.0 | merge (no push) |
| 5 | 1 | 7.0 | 7.0 > 1.0 | push 7.0 |
| 3 | 3 | 3.0 | 3.0 ≤ 7.0 | merge (no push) |
| 0 | 1 | 12.0 | 12.0 > 7.0 | push 12.0 |

Stack = [1.0, 7.0, 12.0] → **3 fleets**

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n log n) | Dominated by sorting step |
| **Space** | O(n) | Stack holds at most n fleet arrival times |

---

## Follow-Up Questions

**Q: Why use `zip` here instead of `enumerate`?**
`zip(position, speed)` pairs corresponding elements from two separate lists — that's what we need. `enumerate` only pairs an index with one list's element, so it wouldn't give us both position and speed together.

**Q: What if two cars have the same arrival time?**
They form a fleet together. The condition `time > stack[-1]` (strictly greater) correctly handles this: equal time means the car merges rather than forming a new fleet.

**Q: Can you solve this without a stack?**
Yes — just a counter and a `max_time` variable. Whenever `time > max_time`, increment fleet count and update max_time. The stack is conceptually cleaner and matches the standard fleet-counting mental model.
