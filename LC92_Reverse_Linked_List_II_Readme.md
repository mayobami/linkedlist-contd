# LeetCode 92 — Reverse Linked List II

## Problem
Reverse the nodes of a singly linked list from position `left` to `right` in **one pass**. Return the reversed list.

**Example:**
```
Input:  1 → 2 → 3 → 4 → 5,  left = 2, right = 4
Output: 1 → 4 → 3 → 2 → 5
```

---

## Interview Strategy

### Clarifying Questions to Ask
- Can `left` equal `right`? *(Yes — return list unchanged)*
- Is `left` always ≥ 1 and `right` always ≤ list length? *(Yes per constraints)*
- Can `left` be 1 (reverse from the very start)? *(Yes — dummy node handles this)*

### How to Talk Through It
> "I'll use a **dummy node** as a sentinel before the head — this cleanly handles the edge case where `left = 1` without special-casing the head pointer. I'll walk `prev` to the node just before position `left`, then use the **insert-to-front** technique: I repeatedly grab the node right after `curr` and insert it right after `prev`. After `(right − left)` iterations the sublist is fully reversed, all in one pass with O(1) space."

### Algorithm Steps
1. Create `dummy → head`; set `prev = dummy`
2. Advance `prev` exactly `(left − 1)` times → now at the node *before* position `left`
3. Set `curr = prev.next` — this is the **tail** of the reversed section (stays fixed)
4. Repeat `(right − left)` times:
   - `next_node = curr.next`
   - `curr.next = next_node.next`   ← detach `next_node`
   - `next_node.next = prev.next`   ← next_node points to front of reversed section
   - `prev.next = next_node`        ← insert next_node right after prev
5. Return `dummy.next`

### Solution (Python)
```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy

        for _ in range(left - 1):
            prev = prev.next

        curr = prev.next

        for _ in range(right - left):
            next_node = curr.next
            curr.next = next_node.next
            next_node.next = prev.next
            prev.next = next_node

        return dummy.next
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | At most `right` steps — single pass |
| **Space** | O(1) | Only a fixed number of pointer variables |

---

## Edge Cases
| Case | Behavior |
|---|---|
| `left == right` | Loop runs 0 times — list unchanged ✓ |
| `left == 1` | Dummy node acts as prev — no special case needed ✓ |
| `left = 1, right = n` | Entire list reversed ✓ |
| Two-node list | Same logic applies ✓ |

---

## Follow-up Questions

**Q: Can you do it recursively?**
Yes — but recursion uses O(n) stack space. The iterative approach is strictly better here.

**Q: What if you need to reverse every k nodes? (LeetCode 25 — Reverse Nodes in k-Group)**
Apply the same insert-to-front logic `(k−1)` times per group, then advance `prev` to the tail of that group and repeat for the next group.

**Q: Why does `curr` stay fixed and never move?**
`curr` is the *tail* of the reversed section. Each iteration moves the node after `curr` to the front of the section. After all iterations, `curr` ends up as the last node in the reversed range — exactly where it should be.

**Q: What is the loop invariant?**
At the start of each iteration: `prev → [reversed section] → curr → [unreversed section]`. Each iteration removes the head of the unreversed section and prepends it to the reversed section.

---

## Key Insight
The dummy node is not just a convenience — it **unifies** the `left = 1` case with all other cases so you write one clean loop instead of branching. The insert-to-front pattern is the canonical O(1)-space in-place sublist reversal technique and appears in many linked-list problems.
