# LeetCode 203 — Remove Linked List Elements (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next   # forward pointer
        self.prev = prev   # backward pointer
```

## Problem
Remove all nodes from a **doubly linked list** whose value equals `val`. Return the new head.

**Example:**
```
Input:  1 ⇄ 2 ⇄ 6 ⇄ 3 ⇄ 4 ⇄ 5 ⇄ 6,  val = 6
Output: 1 ⇄ 2 ⇄ 3 ⇄ 4 ⇄ 5
```

---

## Why DLL Makes This Better

In a singly linked list, to delete a node you need a pointer to the **previous** node — that's why we track `prev` separately. In a DLL, **every node already knows its predecessor via `.prev`**, so deletion is a direct 2-pointer update with no separate tracking variable needed.

---

## Interview Strategy

### Clarifying Questions to Ask
- Can the list be empty? *(Yes — return null)*
- Can val appear multiple times? *(Yes — remove all of them)*
- Can the head itself have val? *(Yes — dummy node handles this)*
- Do we need to maintain the .prev pointers after deletion? *(Yes — it must stay a valid DLL)*

### How to Talk Through It
> "I'll use a dummy node before head so I never have to special-case head removal. I walk `curr` through the list. When `curr.next.val == val`, I grab the target node and perform a DLL deletion: `curr.next = target.next` and if `target.next` exists, `target.next.prev = curr`. The key DLL difference: I must update **both** directions — the forward skip AND the backward backlink."

### Algorithm Steps
1. Create `dummy`; set `dummy.next = head`, `head.prev = dummy`
2. `curr = dummy`
3. While `curr.next` exists:
   - If `curr.next.val == val`:
     - `to_remove = curr.next`
     - `curr.next = to_remove.next`  ← forward skip
     - If `to_remove.next`: `to_remove.next.prev = curr`  ← backward backlink
     - Isolate `to_remove` (set its next/prev to None)
   - Else: `curr = curr.next`
4. Return `dummy.next`; if result exists, set `result.prev = None`

### Solution (Python)
```python
class Solution:
    def removeElements(self, head: Optional[DLLNode], val: int) -> Optional[DLLNode]:
        dummy = DLLNode(0)
        dummy.next = head
        if head:
            head.prev = dummy

        curr = dummy
        while curr.next:
            if curr.next.val == val:
                to_remove = curr.next
                curr.next = to_remove.next
                if to_remove.next:
                    to_remove.next.prev = curr
                to_remove.next = to_remove.prev = None  # isolate
            else:
                curr = curr.next

        if dummy.next:
            dummy.next.prev = None  # head has no real prev
        return dummy.next
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Single pass through the list |
| **Space** | O(1) | Only pointer variables — no extra data structures |

---

## Edge Cases
| Case | Behavior |
|---|---|
| Empty list | dummy.next is null — loop doesn't run, return null ✓ |
| Head has val | dummy absorbs it — curr.next = head.next, no special case ✓ |
| All nodes have val | Every node removed, return null ✓ |
| Last node has val | to_remove.next is null — skip backlink update ✓ |
| Consecutive matching nodes | Loop stays at curr (doesn't advance), removes all ✓ |

---

## Follow-up Questions

**Q: What is different from the SLL version?**
SLL version only needs `curr.next = to_remove.next`. DLL requires one additional line: `to_remove.next.prev = curr`. That backlink update is the essence of every DLL mutation.

**Q: Why do we set `to_remove.next = to_remove.prev = None`?**
Best practice to prevent memory leaks and dangling references. The node is logically deleted — we make it physically isolated too.

**Q: Why set `dummy.next.prev = None` at the end?**
The head of a proper DLL has no predecessor. During traversal, dummy became `head.prev`. Cleaning it up ensures the returned list is a valid DLL.

**Q: Can you delete in O(1) if given a direct pointer to the node?**
Yes — this is the key DLL advantage. Given a direct reference `node`, you can delete it in O(1): `node.prev.next = node.next; if node.next: node.next.prev = node.prev`. No traversal needed. SLL requires O(n) to find the predecessor.

---

## Key Insight
The DLL advantage for deletion: **you never need a "previous" tracking variable** because every node carries its own `.prev` pointer. The mutation pattern for any DLL deletion is always exactly two updates: `predecessor.next = successor` and `successor.prev = predecessor`.
