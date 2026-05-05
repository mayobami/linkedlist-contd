# LeetCode 2 — Add Two Numbers (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev
```

## Problem
Two non-negative integers are represented as **doubly linked lists with digits in forward order** (most significant digit first). Add the two numbers and return the sum as a DLL in forward order.

**Example:**
```
Input:  3 ⇄ 4 ⇄ 2   (represents 342)
        4 ⇄ 6 ⇄ 5   (represents 465)
Output: 8 ⇄ 0 ⇄ 7   (represents 807)
```

> Note: The standard LC2 stores digits in **reverse** order (least significant first). This DLL version uses **forward order**, which is more natural to read. The DLL's `.prev` pointer lets us traverse from the tail (LSB) backwards — making addition straightforward without reversing the lists.

---

## Why DLL Enables Forward-Order Addition

In the SLL version, digits are stored LSB-first so you can add from head to head. With DLL, you can store digits MSB-first (natural reading order) and **walk backwards from the tail using `.prev`** to start from the least significant digit. No reversal of the input lists needed.

---

## Interview Strategy

### Clarifying Questions to Ask
- Are digits stored most-significant-first? *(Yes, in this DLL version)*
- Can there be leading zeros? *(No, except for the number 0 itself)*
- Can lists have different lengths? *(Yes — handle carry carefully)*

### How to Talk Through It
> "Since digits are in forward order, I use `.prev` to walk backwards from the tail of each list, adding digit by digit with a carry. I build the result as a DLL in forward order by prepending each new digit — inserting at the front. This gives the result in the correct MSB-first order without any reversal."

### Algorithm Steps
1. Walk `tail1` to the tail of `l1`, `tail2` to the tail of `l2`
2. `p1 = tail1`, `p2 = tail2`, `carry = 0`, `result_head = None`
3. While `p1` or `p2` or `carry`:
   - `total = carry + (p1.val if p1 else 0) + (p2.val if p2 else 0)`
   - `carry, digit = divmod(total, 10)`
   - Create `new_node(digit)`, prepend to result: `new_node.next = result_head`; if `result_head`: `result_head.prev = new_node`; `result_head = new_node`
   - `p1 = p1.prev if p1 else None`
   - `p2 = p2.prev if p2 else None`
4. Return `result_head`

### Solution (Python)
```python
class Solution:
    def addTwoNumbers(self, l1: Optional[DLLNode], l2: Optional[DLLNode]) -> Optional[DLLNode]:
        # Find tails (least significant digits)
        tail1 = l1
        while tail1.next:
            tail1 = tail1.next

        tail2 = l2
        while tail2.next:
            tail2 = tail2.next

        p1, p2 = tail1, tail2
        carry = 0
        result_head = None

        while p1 or p2 or carry:
            total = carry
            if p1:
                total += p1.val
                p1 = p1.prev       # walk backwards using DLL!
            if p2:
                total += p2.val
                p2 = p2.prev       # walk backwards using DLL!

            carry, digit = divmod(total, 10)

            # Prepend to result (builds MSB-first order naturally)
            new_node = DLLNode(digit)
            new_node.next = result_head
            if result_head:
                result_head.prev = new_node
            result_head = new_node

        return result_head
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(max(m, n)) | Find tails O(m+n) + addition loop O(max(m,n)) |
| **Space** | O(max(m, n)) | Result list has at most max(m,n)+1 nodes |

---

## Edge Cases
| Case | Behavior |
|---|---|
| Different lengths | Shorter list's p reaches None — add 0 for remaining digits ✓ |
| Final carry | Loop continues with `p1=None, p2=None, carry=1` — prepends extra digit ✓ |
| Both single nodes | One addition step, carry handled ✓ |
| Sum = 0 | `total=0, carry=0, digit=0` — result is `[0]` ✓ |

---

## Follow-up Questions

**Q: Why prepend instead of append the result digits?**
We add from LSB to MSB (right to left). The first digit we compute is the least significant. By prepending each digit, the last digit computed (MSB) ends up at the front — giving MSB-first order naturally.

**Q: What is the SLL approach to this problem?**
Standard LC2 SLL stores digits LSB-first so you can add from head to head — no backward traversal needed. The DLL version with forward-order storage is the "natural reading" representation, made possible by `.prev`.

**Q: What if you wanted to add in forward order (MSB first) without backward traversal?**
Convert to integers and add, or use a stack to simulate backward traversal. DLL's `.prev` does this implicitly and elegantly.

**Q: Why build result by prepending rather than tracking position?**
Prepending to a DLL is O(1) — just update the new node's `.next = result_head` and `result_head.prev = new_node`. It's the idiomatic DLL equivalent of a stack push.

---

## Key Insight
The DLL's `.prev` pointer turns a problem that normally requires reversing inputs (or storing LSB-first) into a straightforward backward traversal. This is the DLL equivalent of using a stack — instead of pushing to a stack and popping, you just walk `.prev`. Any problem that naturally works right-to-left on a forward-stored sequence benefits from this pattern.
