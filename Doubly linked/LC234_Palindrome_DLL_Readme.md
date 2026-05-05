# LeetCode 234 — Palindrome Linked List (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev
```

## Problem
Given a **doubly linked list**, determine if it is a palindrome (reads the same forwards and backwards).

**Example:**
```
Input:  1 ⇄ 2 ⇄ 2 ⇄ 1   →  True
Input:  1 ⇄ 2 ⇄ 3         →  False
```

---

## Why DLL Makes This Much Simpler

The standard SLL solution requires: find middle → reverse second half → compare → (optionally restore). That's O(n) time but destroys the list structure temporarily.

With a DLL, you already have access to the tail via `.prev` traversal. **Use two pointers — one from the front, one from the back — and walk inward simultaneously**. No reversal. No mutation. The list stays intact.

---

## Interview Strategy

### Clarifying Questions to Ask
- Is the list guaranteed to be a valid DLL (`.prev` correctly set)? *(Yes)*
- Can the list be empty or single node? *(Yes — both are palindromes)*
- Can we modify the list? *(Not needed with DLL — we can check without mutating)*

### How to Talk Through It
> "Since it's a DLL, I can reach the tail in O(n) using `.next`, then use two pointers — `left` starting at head, `right` starting at tail — and walk them inward comparing values. The DLL's `.prev` pointer lets me move `right` backwards for free. I stop when `left` and `right` meet or cross. No reversal needed."

### Algorithm Steps
1. Handle base cases: empty list or single node → return True
2. Walk `tail = head` forward until `tail.next is None`
3. `left = head`, `right = tail`
4. While `left != right` and `left.prev != right`:
   - If `left.val != right.val` → return False
   - `left = left.next`
   - `right = right.prev`  ← only possible because of DLL!
5. Return True

### Solution (Python)
```python
class Solution:
    def isPalindrome(self, head: Optional[DLLNode]) -> bool:
        if not head or not head.next:
            return True

        # Find tail
        tail = head
        while tail.next:
            tail = tail.next

        # Two-pointer from both ends
        left, right = head, tail
        while left != right and left.prev != right:
            if left.val != right.val:
                return False
            left = left.next
            right = right.prev      # ← the DLL superpower

        return True
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Find tail O(n) + inward scan O(n/2) |
| **Space** | O(1) | Only two pointer variables |

### Comparison with SLL
| | SLL | DLL |
|---|---|---|
| Time | O(n) | O(n) |
| Space | O(1) | O(1) |
| Mutates list? | Yes (reversal) | **No** |
| Code simplicity | Complex | **Simple** |

---

## Edge Cases
| Case | Behavior |
|---|---|
| Empty list | Base case returns True ✓ |
| Single node | Base case returns True ✓ |
| Two nodes, same | `left != right` is True, compare, both advance, `left.prev == right` stops loop ✓ |
| Two nodes, different | First comparison fails, return False ✓ |
| Odd length palindrome | `left == right` when they meet at the middle node ✓ |
| Even length palindrome | `left.prev == right` when they cross ✓ |

---

## Follow-up Questions

**Q: What is the stopping condition `left.prev != right`?**
For even-length lists, `left` and `right` will never be the same node — they'll swap past each other. After the last valid comparison, `left` advances to the node that `right` was on, and `right` retreats to the node that `left` was on. At that point `left.prev == right` (they've crossed). We stop to avoid comparing the same pair again.

**Q: Why not use a stack?**
A stack would give O(n) space. The two-pointer approach is O(1) space because `.prev` gives us the backward traversal for free.

**Q: In the SLL version, why do we need to reverse?**
SLL nodes don't know their predecessor. The only way to traverse backwards is to reverse the second half first. DLL eliminates this entirely.

**Q: How would you build this DLL from a regular list of values?**
Create nodes left to right. After `new_node.next = None`, also set `new_node.prev = prev_node` as you go. Build it in one pass: O(n) time, O(n) space for the DLL nodes themselves.

---

## Key Insight
This problem shows the single most powerful DLL advantage: **backward traversal is O(1) per step**. In SLL, going backwards requires reversing. In DLL, `right = right.prev` is all you need. Any problem that benefits from simultaneous front-and-back access is a natural DLL problem — palindrome check, reorder list, finding median, etc.
