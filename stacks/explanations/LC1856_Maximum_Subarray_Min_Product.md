# LC 1856 — Maximum Subarray Min-Product

## Problem Summary

The **min-product** of a subarray is defined as `(minimum value of subarray) × (sum of subarray)`. Given an array `nums`, return the **maximum min-product** of any non-empty subarray, modulo 10^9 + 7.

**Example:**
```
Input:  nums = [1, 2, 3, 2]
Output: 14
Explanation: Subarray [2,3,2] has min=2, sum=7, product=14
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to find the subarray whose min-value times its total sum is maximized — and return that product modulo 10^9+7."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Are all values positive? That's key — if negatives are possible, a wider subarray might lower the min dramatically."
- "What's the range of values and array size? This affects whether prefix sums fit in standard integers."
- "Should the modulo apply only to the final answer, or to intermediate calculations too?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force is O(n³): try all subarrays, find the min and sum of each. O(n²) with prefix sums to speed up the sum part. But we still need O(1) min lookup — and that requires a monotonic stack to get to O(n)."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach combines two techniques: **prefix sums** to compute any subarray sum in O(1), and a **monotonic stack** to find, for each element, the leftmost and rightmost boundary where it is the minimum. For each element as the minimum, the optimal subarray extends as far left and right as possible before hitting a smaller element. The stack gives us these boundaries when each element is popped."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space — one pass for prefix sums, one pass with the stack."

### Step 6 — Code with Narration (~5–8 minutes)
> "First I build prefix sums so that sum from index l to r = prefix[r+1] - prefix[l]..."
> "Then I iterate with a sentinel index i up to n (inclusive) — when i=n, it acts as a virtual bar of height -infinity to flush the stack..."
> "When a popped element mid is the minimum: left boundary is stack[-1]+1 (or 0 if stack empty), right boundary is i-1. Sum = prefix[i] - prefix[left]..."
> "I track the max across all popped elements and apply modulo at the end..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "nums=[1,2,3,2]. prefix=[0,1,3,6,8]. i=0: push 0. i=1: push 1. i=2: push 2. i=3: nums[2]=3 >= nums[3]=2 → pop mid=2. left=stack[-1]+1=2, right=2. sum=prefix[3]-prefix[2]=6-3=3. product=3×3=9. i=3: push 3. i=4 (flush): nums[3]=2 → pop mid=3. left=stack[-1]+1=2, right=3. sum=prefix[4]-prefix[2]=8-3=5. Wait — left=stack[-1]+1. After pop(2), stack=[0,1]. stack[-1]=1. left=2. sum=prefix[4]-prefix[2]=8-3=5. product=2×5=10... Hmm let me recheck. After pop mid=3: stack has [0,1]. left=stack[-1]+1=2, i=4. sum=prefix[4]-prefix[2]=8-3=5. 2×5=10. Pop mid=1: stack=[0]. left=1, sum=prefix[4]-prefix[1]=8-1=7. 2×7=14. Pop mid=0: stack=[]. left=0, sum=prefix[4]-prefix[0]=8. 1×8=8. Max=14."

### Step 8 — Handle Edge Cases (~1 minute)
> "Single element: sum equals that element, min equals that element, product = element². Array of all equal values: the entire array is the optimal subarray. The sentinel i=n flushes all remaining stack entries against the right wall."

---

## Mental Model / Intuition

For each element as the **minimum** of some subarray, you want the widest possible subarray that still has it as the minimum — because a wider subarray means a larger sum.

The widest valid window is: extend left until you hit something smaller, extend right until you hit something smaller. This is exactly the "previous smaller" and "next smaller" problem — which a monotonic stack solves in O(n).

The trick of iterating up to `n` (one past the end) with a sentinel acts as a virtual bar of height zero that forces all remaining elements off the stack at the end, closing all open windows simultaneously.

Prefix sums turn the "sum of subarray from l to r" query from O(n) to O(1): `prefix[r+1] - prefix[l]`.

---

## Approach

1. Build prefix sums: `prefix[i+1] = prefix[i] + nums[i]`.
2. Use a monotonic increasing stack (stores indices).
3. For `i` from 0 to n (inclusive):
   - While `stack` is non-empty and (`i == n` or `nums[stack[-1]] >= nums[i]`):
     - Pop `mid = stack.pop()`
     - `left = stack[-1] + 1` if stack else `0`
     - `total = prefix[i] - prefix[left]`
     - Update `res = max(res, nums[mid] * total)`
   - Push `i`
4. Return `res % (10^9 + 7)`.

---

## Code

```python
def maxSumMinProduct(self, nums):
    MOD = 10**9 + 7
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i+1] = prefix[i] + nums[i]

    stack = []
    res = 0
    for i in range(n + 1):
        while stack and (i == n or nums[stack[-1]] >= nums[i]):
            mid = stack.pop()
            left = stack[-1] + 1 if stack else 0
            total = prefix[i] - prefix[left]
            res = max(res, nums[mid] * total)
        stack.append(i)

    return res % MOD
```

---

## Step-by-Step Walkthrough

Input: `nums=[1,2,3,2]`, `prefix=[0,1,3,6,8]`

| i | Action | mid | left | right (i-1) | sum | product | res |
|---|--------|-----|------|-------------|-----|---------|-----|
| 0 | push 0 | — | — | — | — | — | 0 |
| 1 | push 1 | — | — | — | — | — | 0 |
| 2 | push 2 | — | — | — | — | — | 0 |
| 3 | pop mid=2 (nums[2]=3 ≥ nums[3]=2) | 2 | 2 | 2 | prefix[3]-prefix[2]=3 | 3×3=9 | 9 |
| 3 | push 3 | — | — | — | — | — | 9 |
| 4 (flush) | pop mid=3 | 3 | 2 | 3 | prefix[4]-prefix[2]=5 | 2×5=10 | 10 |
| 4 (flush) | pop mid=1 | 1 | 1 | 3 | prefix[4]-prefix[1]=7 | 2×7=**14** | **14** |
| 4 (flush) | pop mid=0 | 0 | 0 | 3 | prefix[4]-prefix[0]=8 | 1×8=8 | 14 |

Result: `14 % (10^9+7) = 14`

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Prefix sum build O(n) + each index pushed/popped once |
| **Space** | O(n) | Prefix array O(n) + stack O(n) |

---

## Comparison Table

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute force (all subarrays) | O(n³) | O(1) | TLE for large n |
| Prefix sums only | O(n²) | O(n) | Still TLE |
| Prefix sums + monotonic stack | O(n) | O(n) | Optimal |

---

## Follow-Up Questions

**Q: Why does the approach only work for positive numbers?**
With all positives, a wider subarray always has a larger sum. So for each minimum, extending the subarray as wide as possible is always optimal. With negatives, a wider subarray could decrease the sum, making the problem much harder.

**Q: Why iterate to `n` (one past the end) instead of stopping at `n-1`?**
Elements that are still in the stack after processing all of `nums` haven't found a "right boundary" smaller than them. Using `i=n` as a sentinel flushes them, treating the entire remaining array as their right extent. Without it, you'd need a separate flush loop.

**Q: Why apply modulo only at the end, not to intermediate products?**
The modulo only applies to the final answer. Applying it to intermediate values would corrupt `max()` comparisons — `max(a % MOD, b % MOD)` doesn't equal `max(a, b) % MOD` when values exceed MOD. Keep raw values for comparisons, mod only the return value.
