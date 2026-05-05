# LeetCode 21 — Merge Two Sorted Lists (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev
```

## Problem
Merge two sorted **doubly linked lists** into one sorted doubly linked list. Return the head of the merged list. The merged list must be a valid DLL — every node's `.prev` must point to its actual predecessor.

**Example:**
```
Input:  1 ⇄ 2 ⇄ 4   and   1 ⇄ 3 ⇄ 4
Output: 1 ⇄ 1 ⇄ 2 ⇄ 3 ⇄ 4 ⇄ 4
```

---

## Why DLL Adds Complexity Here

Merging two SLLs only requires setting `.next`. For DLL, **every time you attach a node you must also set its `.prev`**. The logic is the same — the pointer bookkeeping doubles. This problem teaches the discipline of never mutating `.next` without also mutating `.prev`.

---

## Interview Strategy

### Clarifying Questions to Ask
- Are input lists guaranteed sorted? *(Yes)*
- Can either list be empty? *(Yes — return the other list)*
- Must `.prev` pointers be correct in the result? *(Yes — valid DLL output)*
- Are there duplicate values? *(Yes — handle with `<=` for stable merge)*

### How to Talk Through It
> "Same dummy-node approach as the SLL merge, but I must update `.prev` every time I attach a node. When I do `curr.next = chosen_node`, I immediately follow it with `chosen_node.prev = curr`. After the loop, I attach the remaining half and set its `.prev`. Finally, the head's `.prev` should be null."

### Algorithm Steps
1. Create `dummy`; `curr = dummy`
2. While both `list1` and `list2` exist:
   - Pick the node with the smaller `.val`
   - `curr.next = chosen`
   - `chosen.prev = curr`  ← DLL-specific
   - Advance the chosen list's pointer
   - `curr = curr.next`
3. `remaining = list1 or list2`
4. `curr.next = remaining`
5. If `remaining`: `remaining.prev = curr`  ← DLL-specific
6. `dummy.next.prev = None`  ← clean up head's prev
7. Return `dummy.next`

### Solution (Python)
```python
class Solution:
    def mergeTwoLists(self, list1: Optional[DLLNode], list2: Optional[DLLNode]) -> Optional[DLLNode]:
        dummy = DLLNode(0)
        curr = dummy

        while list1 and list2:
            if list1.val <= list2.val:
                curr.next = list1
                list1.prev = curr       # ← set backward link
                list1 = list1.next
            else:
                curr.next = list2
                list2.prev = curr       # ← set backward link
                list2 = list2.next
            curr = curr.next

        remaining = list1 or list2
        curr.next = remaining
        if remaining:
            remaining.prev = curr       # ← set backward link for remainder

        dummy.next.prev = None          # head has no predecessor
        return dummy.next
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(m + n) | Visit each node exactly once |
| **Space** | O(1) | Only pointer variables — nodes are rewired in-place |

---

## Edge Cases
| Case | Behavior |
|---|---|
| Both lists empty | Loop skips, remaining=null, return null ✓ |
| One list empty | Loop skips, attach non-null remaining ✓ |
| Equal values | `<=` picks left first — stable sort preserved ✓ |
| Single-node lists | Loop runs once, remainder attached correctly ✓ |

---

## Follow-up Questions

**Q: What is the extra work vs SLL merge?**
Exactly 2 extra `.prev` assignments per loop iteration (one inside the loop, one for the remainder). The time complexity doesn't change — it's still O(m+n) — but DLL merge requires understanding that `.prev` must always mirror `.next` in reverse.

**Q: Why `dummy.next.prev = None` at the end?**
After the loop, `dummy.next.prev` points to `dummy` (because we set `chosen.prev = curr` and `curr` was `dummy` on the first iteration). The real head of a DLL has no predecessor, so we clean this up.

**Q: What if we forget to set `.prev`?**
The result is a valid SLL with dangling `.prev` pointers — traversal from tail to head would be broken, and any algorithm that uses `.prev` (like LC234 palindrome check) would fail.

**Q: Why use a dummy node?**
Without dummy, we'd need to special-case which list's head becomes the merged head. Dummy lets `curr` always have a previous node and eliminates branching.

---

## Key Insight
DLL merge enforces a discipline: **every `.next` assignment has a matching `.prev` assignment**. Think of it as two-way street maintenance — you lay a road forward AND put up a sign pointing back. The algorithm structure is identical to SLL merge; the discipline is in never forgetting the return trip.
