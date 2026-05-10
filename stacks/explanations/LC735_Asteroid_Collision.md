# LC 735 — Asteroid Collision

## Problem Summary

An array `asteroids` represents asteroids in a row. Positive values move right, negative values move left. All move at the same absolute speed. When a right-moving and left-moving asteroid meet, the smaller one explodes; if equal, both explode. Return the state of asteroids after all collisions.

**Example:**
```
Input:  asteroids = [5, 10, -5, -10, -20, 7]
Output: [-20, 7]
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to simulate asteroid collisions in a row — positive values go right, negative go left — and return what remains after all collisions have resolved."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Two positive or two negative asteroids never collide, right? Only right + left can collide?"
- "Can the array contain zeros? I'll assume no based on typical constraints."
- "If a large left-moving asteroid destroys a right-moving one, can it then collide with the next one?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to repeatedly scan the array for adjacent right-left pairs, resolve them, and repeat — O(n²) in the worst case. With a stack we can process each asteroid once: the stack holds right-moving survivors, and a left-moving asteroid fights them in sequence."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is a **stack for right-moving asteroids**. For each new asteroid `a`: if `a > 0` or the stack is empty or stack top is negative, push directly — no collision. If `a < 0` and the stack top is positive, they collide. We compute `diff = a + stack[-1]`. If `diff < 0`, the top is destroyed (pop). If `diff > 0`, `a` is destroyed (set `a = 0`). If `diff == 0`, both are destroyed. Setting `a = 0` elegantly exits the while loop since `0 < 0` is false."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space — each asteroid is pushed and popped at most once."

### Step 6 — Code with Narration (~5–8 minutes)
> "For each asteroid a, I enter a while loop that handles collisions..."
> "The condition `stack and a < 0 and stack[-1] > 0` means: there's a right-mover on the stack, and a is left-moving — collision possible..."
> "diff = a + stack[-1]. If negative, stack top is smaller → pop it. If positive, a is smaller → set a=0 which also exits the loop. If zero, both gone → a=0 and pop..."
> "After the while loop, if a is still nonzero (survived), push it..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "Input [5,10,-5,-10,-20,7]. Push 5. Push 10. a=-5: diff=-5+10=5>0, a=0. a=-10: diff=-10+10=0, both destroyed, a=0. a=-20: stack=[5], diff=-20+5=-15<0, pop 5. Stack empty, push -20. a=7: a>0, push. Result=[-20,7]."

### Step 8 — Handle Edge Cases (~1 minute)
> "All positive: no collisions, stack accumulates everything. All negative: no collisions either — each new negative goes directly onto an empty or negative-top stack. A very large left-mover can chain-destroy multiple right-movers — the while loop handles this naturally."

---

## Mental Model / Intuition

The stack acts as a **waiting area for right-moving asteroids**. They pile up because they're all moving right — away from each other — so they never collide with each other.

When a left-moving asteroid arrives, it's like a wrecking ball heading backward. It fights the most recent right-mover first (LIFO), then the one before that, until it either runs out of fuel (destroyed itself) or clears all right-movers in its path.

The `diff = a + stack[-1]` trick works because both asteroids have opposite signs. If `a = -10` and `stack[-1] = 5`, then `diff = -5 < 0` means the right-mover was smaller. If `diff > 0`, the left-mover was smaller. Zero means equal — mutual destruction.

---

## Approach

1. Initialize `stack = []`.
2. For each asteroid `a`:
   - While `stack and a < 0 and stack[-1] > 0`:
     - Compute `diff = a + stack[-1]`
     - If `diff < 0`: stack top is smaller → `stack.pop()`
     - If `diff > 0`: `a` is smaller → `a = 0` (exits while)
     - If `diff == 0`: both destroyed → `a = 0`, `stack.pop()` (exits while)
   - If `a != 0`: push `a`
3. Return `stack`.

---

## Code

```python
def asteroidCollision(self, asteroids):
    stack = []
    for a in asteroids:
        while stack and a < 0 and stack[-1] > 0:
            diff = a + stack[-1]
            if diff < 0:     # top smaller → top destroyed
                stack.pop()
            elif diff > 0:   # current smaller → current destroyed
                a = 0
            else:            # equal → both destroyed
                a = 0
                stack.pop()
        if a:
            stack.append(a)
    return stack
```

---

## Step-by-Step Walkthrough

Input: `[5, 10, -5, -10, -20, 7]`

| Asteroid | Stack before | Collision? | diff | Stack after |
|----------|-------------|------------|------|-------------|
| 5 | [] | No (a>0) | — | [5] |
| 10 | [5] | No (a>0) | — | [5,10] |
| -5 | [5,10] | Yes | -5+10=5 → a=-5 dies | [5,10] |
| -10 | [5,10] | Yes | -10+10=0 → both die | [5] |
| -20 | [5] | Yes | -20+5=-15 → 5 dies | [-20] |
| 7 | [-20] | No (top<0) | — | [-20,7] |

Result: `[-20, 7]`

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Each asteroid pushed and popped at most once |
| **Space** | O(n) | Stack holds at most n survivors |

---

## Follow-Up Questions

**Q: Why does setting `a = 0` work to exit the while loop?**
The while condition includes `a < 0`. After `a = 0`, that check is false, so the loop exits immediately. It's a clean dual-purpose assignment: marks destruction AND terminates the collision loop.

**Q: What if two negative asteroids are adjacent?**
They move in the same direction (both left), so they never collide. The while condition `stack[-1] > 0` prevents any interaction between same-direction asteroids.

**Q: Can you solve this without a stack?**
Not cleanly — you'd need to repeatedly scan backward through the array looking for right-movers, which is O(n²). The stack gives you O(1) access to the most recent right-mover at all times.
