# LeetCode 23 — Merge K Sorted Lists (Doubly Linked List)

## DLL Node Definition
```python
class DLLNode:
    def __init__(self, val=0, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev
```

## Problem
Given `k` sorted **doubly linked lists**, merge them into one sorted doubly linked list. All `.prev` pointers must be correct in the result.

**Example:**
```
Input:  [1⇄4⇄5,  1⇄3⇄4,  2⇄6]
Output: 1⇄1⇄2⇄3⇄4⇄4⇄5⇄6
```

---

## Why DLL Adds a Layer

The merge logic (min-heap) is identical to the SLL version. The DLL version adds exactly **one extra line per node attached**: `node.prev = curr`. This problem reinforces that DLL operations always require maintaining the backward link alongside the forward one.

---

## Interview Strategy

### Clarifying Questions to Ask
- Can any list be empty or null? *(Yes — skip null heads)*
- Can k be 0? *(Yes — return null)*
- Must `.prev` pointers be valid in the result? *(Yes)*
- Are there duplicate values across lists? *(Yes — stable sort with index tiebreaker)*

### How to Talk Through It
> "I'll use a min-heap seeded with each list's head node. Each pop gives the globally smallest current node. I attach it to my result DLL — setting both `.next` and `.prev` — then push its successor into the heap. The DLL twist: every `curr.next = node` must be followed by `node.prev = curr`. At the end I clean up the result head's `.prev`."

### Two Approaches

#### Approach 1: Min-Heap (Recommended)
- Push all k heads into a heap with `(val, list_index, node)`
- Pop minimum, attach to result (set `.prev`!), push node's `.next`
- Time: O(n log k), Space: O(k) heap

#### Approach 2: Divide & Conquer
- Pair up lists and merge pairs using LC21 DLL merge
- Repeat until one list remains
- Time: O(n log k), Space: O(log k) recursion stack

### Algorithm (Min-Heap)
1. `dummy = DLLNode(0)`, `curr = dummy`
2. Push `(node.val, i, node)` for each non-null head `node` in `lists`
3. While heap not empty:
   - `val, i, node = heappop(heap)`
   - `curr.next = node`; `node.prev = curr`  ← DLL-specific
   - `curr = curr.next`
   - If `node.next`: push `(node.next.val, i, node.next)`
4. If `dummy.next`: `dummy.next.prev = None`  ← clean head
5. Return `dummy.next`

### Solution (Python — Min-Heap)
```python
import heapq

class Solution:
    def mergeKLists(self, lists: List[Optional[DLLNode]]) -> Optional[DLLNode]:
        dummy = DLLNode(0)
        curr = dummy
        heap = []

        for i, node in enumerate(lists):
            if node:
                heapq.heappush(heap, (node.val, i, node))

        while heap:
            val, i, node = heapq.heappop(heap)
            curr.next = node
            node.prev = curr            # ← set backward link
            curr = curr.next
            if node.next:
                heapq.heappush(heap, (node.next.val, i, node.next))

        if dummy.next:
            dummy.next.prev = None      # head has no predecessor
        return dummy.next
```

### Solution (Python — Divide & Conquer)
```python
class Solution:
    def mergeKLists(self, lists: List[Optional[DLLNode]]) -> Optional[DLLNode]:
        if not lists:
            return None
        while len(lists) > 1:
            merged = []
            for i in range(0, len(lists), 2):
                l1 = lists[i]
                l2 = lists[i+1] if i+1 < len(lists) else None
                merged.append(self.mergeTwoDLL(l1, l2))
            lists = merged
        return lists[0]

    def mergeTwoDLL(self, l1, l2):
        dummy = DLLNode(0)
        curr = dummy
        while l1 and l2:
            if l1.val <= l2.val:
                curr.next = l1; l1.prev = curr; l1 = l1.next
            else:
                curr.next = l2; l2.prev = curr; l2 = l2.next
            curr = curr.next
        remaining = l1 or l2
        curr.next = remaining
        if remaining:
            remaining.prev = curr
        if dummy.next:
            dummy.next.prev = None
        return dummy.next
```

---

## Complexity

| | Min-Heap | Divide & Conquer |
|---|---|---|
| **Time** | O(n log k) | O(n log k) |
| **Space** | O(k) heap | O(log k) recursion |
| **Extra DLL work** | +1 line per node | +1 line per node |

where n = total nodes across all lists, k = number of lists.

---

## Edge Cases
| Case | Behavior |
|---|---|
| `lists = []` | Return null ✓ |
| `lists = [null]` | Skip null, heap empty, return null ✓ |
| k = 1 | Return that list (after `.prev` cleanup) ✓ |
| All lists same value | Stable — `i` tiebreaker keeps heap deterministic ✓ |

---

## Follow-up Questions

**Q: Why include `i` (list index) in the heap tuple?**
Python's heap compares tuple elements left-to-right. If two nodes have equal `.val`, Python would try to compare the `DLLNode` objects, which aren't comparable. The index `i` acts as a tiebreaker.

**Q: Why O(n log k) and not O(n log n)?**
The heap always has at most k elements. Each push/pop is O(log k). We do one push/pop per node, so total is O(n log k).

**Q: When does divide & conquer beat the heap?**
When k is very large, the heap's log k overhead is similar. Divide & conquer avoids heap overhead and uses CPU cache better. For small k, heap is simpler to implement.

**Q: What changes if we forget `.prev`?**
The result is a valid SLL but invalid DLL. Backward traversal, palindrome checks, or any algorithm using `.prev` would give wrong results.

---

## Key Insight
Merging k DLLs is algorithmically identical to merging k SLLs — the heap/divide-and-conquer strategy doesn't change. The DLL "tax" is exactly one `.prev` assignment per node appended. This reveals a general principle: **the DLL version of any merge/append algorithm adds exactly one backward-link update per node touched**. The algorithm's complexity and structure are preserved; only the constant factor of pointer work increases.
