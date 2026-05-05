# LeetCode 143 — Reorder List (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev
```

## Problem
Given a **doubly linked list** `L0 ⇄ L1 ⇄ ... ⇄ Ln`, reorder it in-place to:
`L0 ⇄ Ln ⇄ L1 ⇄ Ln-1 ⇄ L2 ⇄ Ln-2 ⇄ ...`

Do not change node values — only rewire the pointers.

**Example:**
```
Input:  1 ⇄ 2 ⇄ 3 ⇄ 4 ⇄ 5
Output: 1 ⇄ 5 ⇄ 2 ⇄ 4 ⇄ 3
```

---

## Why DLL Is Better Here

The SLL approach: find middle → reverse second half → merge alternately. Three distinct phases.

With DLL, you can use **two inward-moving pointers** — `left` from the front and `right` from the tail — to interleave nodes directly. The `.prev` pointer lets `right` retreat without reversal. The list is reordered in a single inward sweep.

---

## Interview Strategy

### Clarifying Questions to Ask
- Is the list guaranteed to be a valid DLL? *(Yes)*
- Reorder in-place? *(Yes — no new nodes)*
- What if list has 0, 1, or 2 nodes? *(0 or 1 = no-op; 2 = already ordered)*
- Must `.prev` pointers stay valid? *(Yes)*

### How to Talk Through It
> "I use two pointers: `left` starts at head, `right` starts at tail. On each iteration: detach `right` from the end (set its old predecessor's `.next = None`), then insert it between `left` and `left.next`, updating all four pointers. Advance `left` two steps. Stop when `left` and `right` meet or cross — the list is half-interleaved at that point."

### Algorithm Steps
1. If ≤ 2 nodes, return
2. Find `right` = tail
3. `left = head`
4. While `left != right` and `left.next != right`:
   a. Save `right_prev = right.prev`, `left_next = left.next`
   b. Detach `right`: `right_prev.next = None`, `right.prev = None`
   c. Insert `right` after `left`:
      - `left.next = right`
      - `right.prev = left`
      - `right.next = left_next`
      - `left_next.prev = right`
   d. Advance: `left = left_next`, `right = right_prev`
5. Done — list is reordered in-place

### Solution (Python)
```python
class Solution:
    def reorderList(self, head: Optional[DLLNode]) -> None:
        if not head or not head.next or not head.next.next:
            return

        # Find tail
        right = head
        while right.next:
            right = right.next

        left = head
        while left != right and left.next != right:
            right_prev = right.prev
            left_next = left.next

            # Detach right from its current position at the end
            right_prev.next = None
            right.prev = None

            # Insert right between left and left_next
            left.next = right
            right.prev = left
            right.next = left_next
            left_next.prev = right

            # Advance both pointers inward
            left = left_next
            right = right_prev
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Find tail O(n) + one inward pass O(n/2) |
| **Space** | O(1) | Only pointer variables |

### Comparison with SLL
| | SLL | DLL |
|---|---|---|
| Phases | 3 (find mid, reverse, merge) | 1 (inward sweep) |
| Mutates structure? | Yes (reversal) | Yes (rewiring only) |
| Code lines | ~15 | ~12 |
| Intuition | Multi-step | Direct two-pointer |

---

## Edge Cases
| Case | Behavior |
|---|---|
| 0 or 1 nodes | Early return ✓ |
| 2 nodes | `left.next == right` → loop stops, no change needed ✓ |
| 3 nodes (odd) | `left == right` stops loop when they meet at middle ✓ |
| 4 nodes (even) | Loop runs twice; `left.next == right` stops ✓ |

---

## Follow-up Questions

**Q: What is the stopping condition `left.next != right`?**
When `left` and `right` are adjacent (separated by no nodes), any further insertion would put `right` between `left` and the node that is already `right` — a no-op. We stop here because the remaining center segment needs no reordering.

**Q: Why must we save `right_prev` and `left_next` before rewiring?**
Once we start changing pointers, the old connections are gone. Saving them beforehand is the same principle as saving `next_node` before cutting a pointer in any linked list insertion.

**Q: In the SLL version, why can't we use the two-pointer directly?**
SLL has no `.prev` — once `right` is inserted in the middle of the list, we have no way to go back to `right`'s old predecessor to advance `right` inward. DLL makes `right_prev = right.prev` trivially available.

**Q: What invariant holds after each iteration?**
`left` and `right` move one step closer to the center. The already-processed prefix is correctly reordered. The unprocessed center is the still-linked sublist between `left` and `right`.

---

## Key Insight
This problem collapses from 3 phases (SLL) to 1 phase (DLL) because the DLL's `.prev` pointer eliminates the reversal phase entirely. **Anything that requires "starting from the back" in SLL becomes a simple `.prev` access in DLL.** The two-pointer inward sweep is the canonical DLL pattern for problems that interleave front and back elements.
