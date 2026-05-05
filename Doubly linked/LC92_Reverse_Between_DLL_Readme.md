# LeetCode 92 — Reverse Linked List II (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev
```

## Problem
Reverse the nodes of a **doubly linked list** from position `left` to `right`. Return the modified list. All `.prev` pointers must remain correct after reversal.

**Example:**
```
Input:  1 ⇄ 2 ⇄ 3 ⇄ 4 ⇄ 5,  left=2, right=4
Output: 1 ⇄ 4 ⇄ 3 ⇄ 2 ⇄ 5
```

---

## Why DLL Is Different

The SLL solution uses the "insert-to-front" trick because SLL nodes can't look backwards — you must carefully redirect 3 pointers per iteration. With a DLL, you can **swap each node's `.next` and `.prev` directly** to reverse it in place, then reconnect the segment's endpoints to the surrounding list. The algorithm is more intuitive because each reversal step is just one swap.

---

## Interview Strategy

### Clarifying Questions to Ask
- Can `left == right`? *(Yes — no-op, return unchanged)*
- Can `left == 1`? *(Yes — dummy handles it)*
- Must `.prev` pointers be valid in output? *(Yes — valid DLL)*

### How to Talk Through It
> "I'll find the node just before position `left` (prev_anchor) and the node after position `right` (next_anchor). Then for each node in [left, right], I swap its `.next` and `.prev` — this reverses its local direction. After swapping, walking `.prev` moves me forward (since `.prev` was `.next` before the swap). Finally, I stitch the reversed segment back: `prev_anchor.next = right_node`, `right_node.prev = prev_anchor`, `left_node.next = next_anchor`, and if `next_anchor` exists, `next_anchor.prev = left_node`."

### Algorithm Steps
1. Create `dummy`; `dummy.next = head`, `head.prev = dummy`
2. Walk `prev_anchor` to node at position `left − 1`
3. `left_node = prev_anchor.next` (will be the tail after reversal)
4. Walk `right_node` from `left_node` exactly `right − left` steps
5. `next_anchor = right_node.next`
6. **Reverse the segment**: for each node from `left_node` to `right_node`:
   - Swap `node.next, node.prev = node.prev, node.next`
   - Move forward: `node = node.prev` (was `.next` before swap)
7. Reconnect: `prev_anchor.next = right_node`, `right_node.prev = prev_anchor`
8. Reconnect: `left_node.next = next_anchor`, if `next_anchor`: `next_anchor.prev = left_node`
9. Return `dummy.next`

### Solution (Python)
```python
class Solution:
    def reverseBetween(self, head: Optional[DLLNode], left: int, right: int) -> Optional[DLLNode]:
        dummy = DLLNode(0)
        dummy.next = head
        head.prev = dummy

        # Step to node before left
        prev_anchor = dummy
        for _ in range(left - 1):
            prev_anchor = prev_anchor.next

        left_node = prev_anchor.next

        # Step to node at right
        right_node = left_node
        for _ in range(right - left):
            right_node = right_node.next

        next_anchor = right_node.next

        # Swap next/prev for each node in segment
        curr = left_node
        while curr != next_anchor:
            curr.next, curr.prev = curr.prev, curr.next
            curr = curr.prev          # move forward (was .next before swap)

        # Reconnect segment to surrounding list
        prev_anchor.next = right_node
        right_node.prev = prev_anchor
        left_node.next = next_anchor
        if next_anchor:
            next_anchor.prev = left_node

        return dummy.next
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Find endpoints O(n) + swap segment O(right−left) |
| **Space** | O(1) | Only pointer variables |

### Comparison with SLL version
| | SLL (insert-to-front) | DLL (swap in-place) |
|---|---|---|
| Pointer changes per iteration | 3 (carefully ordered) | 1 swap (any order) |
| Need to track extra node (next_node)? | Yes | No |
| Conceptual model | Pull and re-insert | Swap direction in place |

---

## Edge Cases
| Case | Behavior |
|---|---|
| `left == right` | Segment has 1 node — swap is identity, reconnect is no-op ✓ |
| `left == 1` | `prev_anchor = dummy` — no special case needed ✓ |
| `right == n` (last node) | `next_anchor = None` — skip `next_anchor.prev` assignment ✓ |
| `left=1, right=n` | Entire list reversed ✓ |

---

## Follow-up Questions

**Q: After swapping `.next` and `.prev`, why do we do `curr = curr.prev`?**
Before the swap, `curr.next` pointed forward (to the next node to process). After the swap, that forward pointer is now in `curr.prev`. So `curr = curr.prev` is still "moving forward" — it just uses the field that now holds the forward pointer.

**Q: Why is the SLL "insert-to-front" trick not used here?**
It doesn't need to be. DLL's `.prev` pointer means each node carries its own backward reference — no need to maintain an external `next_node` variable to avoid losing track of the chain. The swap approach is self-contained.

**Q: Why four reconnect steps at the end (not two)?**
Each side of the segment boundary has two connections: `prev_anchor ↔ right_node` and `left_node ↔ next_anchor`. Each ↔ requires setting both `.next` AND `.prev`. That's 2 pointers × 2 boundaries = 4 assignments.

---

## Key Insight
DLL reversal of a segment reduces to: **swap `.next` and `.prev` for every node in the range, then fix the four boundary connections**. The SLL version needs careful pointer ordering to avoid losing nodes; the DLL version's swap is order-independent because you always have both directions available. The result is code that matches the mental model directly — "reverse" literally means swapping the two directions.
