# LeetCode 148 — Sort List

## Problem
Sort a linked list in **O(n log n)** time and **O(1)** space complexity.

**Example:**
```
Input:  4 → 2 → 1 → 3
Output: 1 → 2 → 3 → 4
```

---

## Interview Strategy

### Clarifying Questions to Ask
- Are there duplicate values? *(Yes — handle them; merge sort is stable)*
- Does O(1) space mean we can't use recursion? *(Usually recursion stack is accepted; mention bottom-up for O(1) follow-up)*
- Any constraints on values? *(Integers, possibly negative)*

### How to Talk Through It
> "I'll use **top-down merge sort**. It's the natural fit for linked lists — splitting is O(n) and merging is O(1) space (just pointer rewiring, no temp array). Quick sort has O(n²) worst case on sorted input and random access is O(n) on a linked list so pivot selection is awkward. The trade-off is O(log n) recursion stack; mention bottom-up merge sort for true O(1) space."

### Algorithm Steps
1. **Base case**: if `head` is null or `head.next` is null → return `head`
2. **Find middle**: slow = head, fast = head.next — advance until fast or fast.next is null
3. **Split**: `second = slow.next`, then `slow.next = None` to cut the list in two
4. **Recurse**: `left = sortList(head)`, `right = sortList(second)`
5. **Merge**: merge the two sorted halves using a dummy node — always take the smaller head

### Solution (Python)
```python
class Solution:
    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:
            return head

        # Find middle (fast starts one ahead to split evenly)
        slow, fast = head, head.next
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next

        # Split
        second = slow.next
        slow.next = None

        # Recurse
        left = self.sortList(head)
        right = self.sortList(second)

        # Merge
        dummy = ListNode(0)
        curr = dummy
        while left and right:
            if left.val <= right.val:
                curr.next = left
                left = left.next
            else:
                curr.next = right
                right = right.next
            curr = curr.next

        curr.next = left or right
        return dummy.next
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n log n) | log n split levels × O(n) merge work per level |
| **Space** | O(log n) | Recursion call stack depth |
| **Space (bottom-up)** | O(1) | Iterative — no recursion stack |

---

## Edge Cases
| Case | Behavior |
|---|---|
| Empty list | Base case returns null ✓ |
| Single node | Base case returns head ✓ |
| Already sorted | Still O(n log n) — no worst-case degradation ✓ |
| All same values | Stable merge keeps them in order ✓ |
| Two nodes | Splits into [a] and [b], merges correctly ✓ |

---

## Follow-up Questions

**Q: Can you do it in O(1) space (no recursion)?**
Yes — **bottom-up merge sort**: start by merging adjacent pairs of size 1, then size 2, 4, 8... until the step size exceeds the list length. Uses a dummy head and careful pointer stitching. No recursion stack.

**Q: Why not quicksort?**
Random access is O(n) on a linked list (can't pick middle in O(1)). Worst case with a bad pivot is O(n²). Merge sort is guaranteed O(n log n) and cleaner to implement.

**Q: Why does `fast` start at `head.next` instead of `head`?**
For a 2-node list [a → b]: if both start at head, slow advances to b and you split into [a→b] and [] — infinite recursion. Starting fast one step ahead ensures slow stops at a, splitting into [a] and [b].

**Q: What is the merge step's invariant?**
`dummy` always heads a sorted list of all processed nodes. `curr` is its tail. Each step we consume the smaller of `left.val` and `right.val` and advance that pointer.

**Q: How does this compare to sorting an array?**
Array merge sort needs an O(n) auxiliary array to merge. Linked list merge is O(1) space — we rewire pointers instead of copying values. However, arrays have better cache locality.

**Q: Is this sort stable?**
Yes. The merge step uses `<=` so equal elements from the left half come first, preserving their original relative order.

---

## Key Insight
Starting `fast = head.next` (one node ahead of `slow`) is the critical detail — it guarantees `slow` stops at the *left-center* node for both odd and even length lists, giving balanced splits. Merge sort is the canonical solution for linked list sorting because the merge step is free of extra memory: you're just rewiring `.next` pointers.
