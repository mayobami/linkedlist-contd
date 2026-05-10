# LC 155 — Min Stack

## Problem Summary

Design a stack that supports `push`, `pop`, `top`, and `getMin` — all in **O(1) time**. The challenge is that a standard stack can return the top in O(1), but retrieving the minimum naively requires scanning the whole stack (O(n)).

**Example:**
```
Input:  push(5), push(3), push(7), push(2), getMin(), pop(), getMin(), top()
Output: 2, 3, 7
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to design a stack data structure that supports all normal stack operations plus a `getMin()` method, and all four operations must run in constant time."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Is `getMin()` called on an empty stack, or can I assume the stack is non-empty when called?"
- "Are there duplicate values? For example, push(3), push(3), pop — should getMin still return 3?"
- "Are there memory constraints, or can I use extra space?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to scan the entire stack for the minimum on every `getMin()` call — that's O(n) per call. We can do better with a mirror stack that tracks the running minimum."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is to use **two parallel stacks**: the main stack for normal operations, and a `min_stack` where each position stores the minimum of all elements at or below that level. Every push appends to both stacks, every pop removes from both. `getMin()` just reads `min_stack[-1]`."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(1) per operation and O(n) total space — we store each element twice."

### Step 6 — Code with Narration (~5–8 minutes)
> "Here I initialize both stacks as empty lists..."
> "In push, I append val to main stack, then compute cur_min — it's val if min_stack is empty, otherwise min(val, min_stack[-1])..."
> "In pop, I remove from both stacks simultaneously so they stay in sync..."
> "top() just returns stack[-1], getMin() returns min_stack[-1] — both O(1)..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "push(5): stack=[5], min_stack=[5]. push(3): stack=[5,3], min_stack=[5,3]. push(7): min(7,3)=3, stack=[5,3,7], min_stack=[5,3,3]. getMin()=min_stack[-1]=3. pop(): both stacks remove 7, min_stack[-1] still 3. Correct."

### Step 8 — Handle Edge Cases (~1 minute)
> "Pushing a value equal to the current min: min(3, 3)=3, so min_stack gets 3 again. After popping the top 3, min_stack still has 3 below. Duplicates are handled correctly because we always take `min(val, prev_min)`, not `<`."

---

## Mental Model / Intuition

Think of `min_stack` as a **snapshot history of the minimum**. At each depth, it answers: "If you were standing at this level looking down, what is the smallest element you can see?" This never changes as you push new items — it only changes if the new item is smaller.

The key insight: when you pop a value, the minimum might have been that value — but `min_stack` already has the answer for the level below it. No recomputation needed.

---

## Approach

1. Maintain two stacks: `stack` (normal) and `min_stack` (running minimum).
2. `push(val)`: append `val` to `stack`; compute `cur_min = min(val, min_stack[-1])` (or `val` if empty) and append to `min_stack`.
3. `pop()`: pop from both stacks simultaneously.
4. `top()`: return `stack[-1]`.
5. `getMin()`: return `min_stack[-1]`.

---

## Code

```python
class MinStack:
    def __init__(self):
        self.stack     = []
        self.min_stack = []

    def push(self, val: int):
        self.stack.append(val)
        cur_min = val if not self.min_stack \
                  else min(val, self.min_stack[-1])
        self.min_stack.append(cur_min)

    def pop(self):
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

---

## Step-by-Step Walkthrough

| Operation | stack | min_stack | Returns |
|-----------|-------|-----------|---------|
| push(5) | [5] | [5] | — |
| push(3) | [5,3] | [5,3] | — |
| push(7) | [5,3,7] | [5,3,3] | — |
| push(2) | [5,3,7,2] | [5,3,3,2] | — |
| getMin() | [5,3,7,2] | [5,3,3,2] | **2** |
| pop() | [5,3,7] | [5,3,3] | — |
| getMin() | [5,3,7] | [5,3,3] | **3** |
| top() | [5,3,7] | [5,3,3] | **7** |

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(1) per operation | All operations are array appends/reads |
| **Space** | O(n) | Two stacks, each at most n elements |

---

## Comparison Table

| Approach | getMin Time | Space | Notes |
|----------|-------------|-------|-------|
| Scan whole stack | O(n) | O(1) extra | Simple but too slow |
| Parallel min_stack | O(1) | O(n) extra | Optimal |
| Store (val, min) tuples | O(1) | O(n) extra | Same idea, single stack |

---

## Follow-Up Questions

**Q: Can you do it with one stack instead of two?**
Yes — push tuples `(val, current_min)` onto a single stack. `getMin()` returns `stack[-1][1]`. Same O(1) time and O(n) space, just different packaging.

**Q: What if there are many duplicate minimums?**
No problem — `min(val, prev_min)` stores the same min value repeatedly. When duplicates are popped, the min_stack still correctly reflects the minimum below.

**Q: What if getMin is rarely called — is there a lazy approach?**
You could recompute on demand (O(n) per call) but save space. Only worth it if getMin is extremely rare and memory is critical. For interview purposes, the two-stack solution is always preferred.
