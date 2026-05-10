# LC 22 — Generate Parentheses

## Problem Summary

Given `n` pairs of parentheses, generate all combinations of **well-formed parentheses**. A string is well-formed if every open bracket has a matching close bracket in the correct order.

**Example:**
```
Input:  n = 2
Output: ["(())", "()()"]
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to generate every valid combination of n pairs of parentheses — no invalid strings like ')(' or '((' with unclosed brackets."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Should the output be in any specific order?"
- "What is the range of n? This affects whether I need to worry about output size."
- "Should I return strings, or is another format acceptable?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to generate all 2^(2n) binary strings of `(` and `)`, then filter only valid ones — hugely wasteful. Instead, I can prune during generation: only make choices that keep the string valid. That's backtracking."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is **backtracking with two counters**: `open` counts how many `(` we've added, `close` counts how many `)`. At each step, we can add `(` only if `open < n`, and add `)` only if `close < open`. These two rules guarantee we only build valid strings. When the string reaches length 2n, it's complete."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "Time is O(4^n / √n) — that's the nth Catalan number, counting how many valid strings exist. Space is O(n) for the recursion stack depth."

### Step 6 — Code with Narration (~5–8 minutes)
> "I initialize a result list, then define a recursive backtrack function that takes the current string, open count, and close count..."
> "Base case: if the string length equals 2n, append to result..."
> "Two branches: add `(` if open < n, add `)` if close < open — these are the validity constraints..."
> "Then I call backtrack with empty string and zero counts..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "For n=2: start with `('', 0, 0)`. open=0 < 2 → add `(` → `('(', 1, 0)`. Again open=1 < 2 → add `(` → `('((', 2, 0)`. Now open=2 = n, can't add more `(`. close=0 < open=2 → add `)` → `('(()', 2, 1)`. close=1 < open=2 → add `)` → `('(())', 2, 2)`. Length=4=2n → add to result. Backtrack and explore `()()` path next."

### Step 8 — Handle Edge Cases (~1 minute)
> "n=1: only `()` is valid. n=0: empty string, result is `['']` — though the problem likely guarantees n ≥ 1. The constraints in the code naturally handle both."

---

## Mental Model / Intuition

Think of building the string as making **binary decisions** at each position. At each step, ask two questions:
- "Can I open more?" — Yes if `open < n`
- "Can I close?" — Yes if `close < open` (you must have something open to close)

The `close < open` rule is the key invariant. It prevents you from ever closing more than you've opened, which is the only way a parentheses string becomes invalid. Backtracking DFS explores every legal path in this decision tree.

---

## Approach

1. Define a recursive `backtrack(s, open, close)` function.
2. Base case: `len(s) == 2 * n` → append to result.
3. If `open < n`: recurse with `s + '('`, `open + 1`.
4. If `close < open`: recurse with `s + ')'`, `close + 1`.
5. Call `backtrack('', 0, 0)` to start.

---

## Code

```python
def generateParenthesis(n: int):
    result = []

    def backtrack(s, open, close):
        if len(s) == 2 * n:
            result.append(s)   # valid!
            return
        if open < n:
            backtrack(s + '(', open+1, close)
        if close < open:
            backtrack(s + ')', open, close+1)

    backtrack('', 0, 0)
    return result
```

---

## Step-by-Step Walkthrough

For n=2, the full decision tree (left branch = add `(`, right branch = add `)`):

```
("", 0, 0)
  → ("(", 1, 0)
      → ("((", 2, 0)
          → ("(()", 2, 1)
              → ("(())", 2, 2) ✓  ADD TO RESULT
      → ("()", 1, 1)
          → ("()(", 2, 1)
              → ("()()", 2, 2) ✓  ADD TO RESULT
```

Result: `["(())", "()()"]`

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(4ⁿ / √n) | nth Catalan number of valid strings, each length 2n |
| **Space** | O(n) | Max recursion depth is 2n (building string of length 2n) |

---

## Comparison Table

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Generate all, filter | O(2^(2n) · n) | O(2n) | Wasteful — most strings invalid |
| Backtracking | O(4ⁿ/√n) | O(n) | Optimal — only generates valid strings |
| DP / iterative | O(4ⁿ/√n) | O(4ⁿ/√n) | Correct but harder to explain in interview |

---

## Follow-Up Questions

**Q: How does the number of valid strings grow with n?**
It follows the nth Catalan number: C(n) = C(2n, n) / (n+1). For n=4 that's already 14 strings. Growth is exponential — unavoidable since we're generating all solutions.

**Q: Can you solve this iteratively instead of recursively?**
Yes — using an explicit stack to simulate the recursion. Each stack frame holds `(s, open, close)`. Same logic, just written with a loop.

**Q: What if we wanted to count valid strings without generating them?**
Just return the nth Catalan number: `math.comb(2*n, n) // (n+1)`. O(1) if computed mathematically.
