# Stack Patterns — When to Use a Stack and Why

## The Core Rule

Use a stack when the problem involves **processing elements in reverse order of arrival** (LIFO), or when you need to **defer a decision about an element until a future element arrives**.

The pattern that almost always signals a stack:

> "The answer for element X depends on something that comes AFTER X in the input."

---

## The Four Stack Trigger Questions

Ask these four questions when you read a problem. If you answer YES to any of them, a stack is likely the right tool.

**1. "Does each element need to wait for a future element to resolve it?"**
- Daily Temperatures: each day waits for its first warmer day
- Online Stock Span: each price waits to see how many prior prices it "absorbs"
- Next Greater Element: each element waits for its first larger neighbor

**2. "Does the problem involve matching or pairing elements?"**
- Valid Parentheses: each `(` waits for its matching `)`
- Evaluate RPN: each operand waits for its operator
- Simplify Path: each directory waits to see if a `..` cancels it

**3. "Does adding a new element invalidate or destroy previous elements?"**
- Asteroid Collision: a left-moving asteroid destroys right-movers behind it
- Remove K Digits: a smaller digit makes previous larger digits removable
- Largest Rectangle: a shorter bar closes the rectangle of all taller bars behind it

**4. "Do I need O(1) access to the most recently seen unresolved item?"**
- Min Stack: need the current minimum at the top
- Car Fleet: need the arrival time of the fleet immediately ahead
- Online Stock Span: need the most recent price that blocks further lookback

---

## General Stack vs. Other Data Structures

| Signal in problem | Use |
|-------------------|-----|
| "Most recent unresolved item" | Stack |
| "Least recently seen item" | Queue |
| "Fastest lookup by key" | HashMap |
| "Minimum/maximum at any time" | Heap |
| "All items in sorted order" | Sorted array / BST |

The key differentiator: a stack is the right choice when **recency** is what matters — the most recently added unresolved element is always the next one to be resolved.

---

## Monotonic Stacks

A **monotonic stack** is a stack with an invariant: elements are always in sorted order (either always increasing or always decreasing from bottom to top).

You maintain this invariant by **popping any element that violates the order** before pushing a new one. The pop event is where the actual work happens.

### Monotonic Decreasing Stack

**Invariant:** Elements decrease from bottom to top (largest at bottom, smallest at top).

**How to maintain it:** Before pushing a new element, pop everything that is **smaller than or equal to** the new element.

**When to use it:**
- Finding the **next greater element** to the right
- Finding the **previous greater element** (what's still in the stack when you push)
- Problems where a larger element "resolves" or "dominates" smaller ones

**Examples:**
- **Daily Temperatures** (LC 739): stack holds indices of days waiting for a warmer day. A new warmer temperature pops all colder days below it.
- **Online Stock Span** (LC 901): stack holds `(price, span)` pairs. A higher price absorbs all lower prices.

**Visual pattern:**
```
Stack (bottom → top): [100, 80, 60]   ← decreasing
New element: 75
Pop 60 (60 < 75), pop 80 (80 > 75 — STOP)
Push 75
Stack: [100, 80, 75]
```

---

### Monotonic Increasing Stack

**Invariant:** Elements increase from bottom to top (smallest at bottom, largest at top).

**How to maintain it:** Before pushing a new element, pop everything that is **greater than or equal to** the new element.

**When to use it:**
- Finding the **next smaller element** to the right
- Finding the **previous smaller element**
- Problems where a smaller element invalidates or "traps" larger ones
- Producing the smallest possible result (greedy digit removal)

**Examples:**
- **Largest Rectangle in Histogram** (LC 84): stack holds bars still "open". A shorter bar closes all taller bars behind it — their right boundary is now known.
- **Remove K Digits** (LC 402): stack holds the greedily smallest digits. A new smaller digit makes previous larger digits removable.
- **Trapping Rain Water**: each bar waits for a taller right wall to trap water.

**Visual pattern:**
```
Stack (bottom → top): [1, 3, 5]   ← increasing
New element: 4
Pop 5 (5 > 4), push 4
Stack: [1, 3, 4]
```

---

### Decreasing vs. Increasing — Decision Rule

| You want to find... | Stack type | Pop condition |
|---------------------|------------|---------------|
| Next **greater** element | Monotonic **decreasing** | pop when new > top |
| Next **smaller** element | Monotonic **increasing** | pop when new < top |
| Previous **greater** element | Monotonic **decreasing** | what remains in stack when you push |
| Previous **smaller** element | Monotonic **increasing** | what remains in stack when you push |
| Maximize result using minimums | Monotonic **increasing** | pop to find each element's left/right boundary |
| Minimize result by removing larger elements | Monotonic **increasing** | pop to greedily remove large digits |

**The mental shorthand:**
- Decreasing stack = "waiting room for elements hoping something BIGGER comes along"
- Increasing stack = "waiting room for elements that get kicked out by something SMALLER"

---

## The "Waiting Room" Mental Model

This is the single most useful way to think about stacks in algorithm problems:

**Every element pushed onto the stack is unresolved — it's waiting for a future element to give it its answer.**

The LIFO property means: the most recently added unresolved element is always resolved first. This is exactly right because in most of these problems, closer elements interact before farther ones do.

When a new element arrives and pops several stack entries at once, that's the payoff — one scan direction resolves many waiting elements. This is why monotonic stacks are O(n) even though it looks like there's a nested loop: each element is pushed once and popped once.

---

## Pattern Recognition Checklist

When you read a problem, scan for these phrases and patterns:

| Phrase / Pattern | Stack signal |
|-----------------|--------------|
| "next greater / smaller element" | Monotonic stack |
| "previous greater / smaller element" | Monotonic stack (look at what's left when you push) |
| "number of consecutive days" | Monotonic decreasing stack with span accumulation |
| "largest rectangle / area" | Monotonic increasing stack (find left/right boundaries) |
| "valid parentheses / brackets" | Plain stack for matching pairs |
| "evaluate expression" | Operand stack (RPN, calculator) |
| "simplify / navigate a path" | Stack for undo (`..` = pop) |
| "remove digits to minimize" | Monotonic increasing stack |
| "collision / destruction" | Stack for survivors |
| "fleet / group merging" | Stack of group arrival times |
| "minimum in O(1)" | Parallel min-stack |
| "subarray minimum × subarray sum" | Monotonic stack for left/right boundaries + prefix sums |

---

## Best Practices

**1. Store indices, not values, when you need position information.**
`answer[j] = i - j` requires knowing j. If you only stored the value, you'd lose the index.

**2. Store tuples when a single value isn't enough.**
LC 901 stores `(price, span)` because the span needs to carry forward across pops. LC 84 stores `(start, height)` because the start index can shift leftward.

**3. The sentinel / flush pattern for monotonic stacks.**
After the main loop, remaining stack entries haven't found their right boundary. Either iterate the remaining entries manually, or add a sentinel value (like `0` for heights, or `n` as a virtual index) to flush everything in the main loop.

**4. The `>= vs >` pop condition matters for duplicates.**
- `stack[-1] >= new` (pop on equal): merges equal elements, avoids double-counting areas
- `stack[-1] > new` (pop only on strictly greater): keeps equal elements as separate entries

**5. amortized O(n) is not obvious — be ready to explain it.**
The nested while-loop looks O(n²) but is actually O(n) amortized. Each element is pushed once and popped once, so total operations ≤ 2n regardless of how many pops happen per iteration.

**6. Always ask: what does an element in the stack REPRESENT?**
- In LC 739: "a day that hasn't found a warmer day yet"
- In LC 84: "a bar whose rectangle is still open on the right"
- In LC 402: "the greedily minimal prefix built so far"

Answering this question clearly is the difference between understanding the algorithm and just memorizing it.

---

## Quick Reference: All Stack Problems in This Repo

| Problem | Stack type | What's stored | Pop trigger |
|---------|-----------|---------------|-------------|
| LC 71 Simplify Path | Plain | directory names | `..` token |
| LC 155 Min Stack | Plain (×2) | values + running min | pop operation |
| LC 22 Generate Parentheses | Implicit (recursion) | open/close counts | base case |
| LC 84 Largest Rectangle | Monotonic increasing | (start, height) | new bar shorter |
| LC 150 Evaluate RPN | Operand stack | integers | operator token |
| LC 402 Remove K Digits | Monotonic increasing | digit characters | new digit smaller |
| LC 735 Asteroid Collision | Survivor stack | right-movers | left-mover arrives |
| LC 739 Daily Temperatures | Monotonic decreasing | indices | warmer temperature |
| LC 853 Car Fleet | Fleet stack | arrival times | slower fleet ahead |
| LC 901 Online Stock Span | Monotonic decreasing | (price, span) | higher price |
| LC 1856 Max Subarray Min-Product | Monotonic increasing | indices | smaller element |
