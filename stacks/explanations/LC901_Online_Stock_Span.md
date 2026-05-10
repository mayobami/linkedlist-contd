# LC 901 — Online Stock Span

## Problem Summary

Design a class `StockSpanner` that calculates the **span** of a stock's price for each new day. The span is defined as the maximum number of consecutive days (including today) where the stock price was less than or equal to today's price.

**Example:**
```
Input:  prices = [100, 80, 60, 70, 60, 75, 85]
Output: [1,    1,  1,  2,  1,  4,  6]
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to design a class that processes stock prices one by one and, for each new price, returns how many consecutive prior days (including today) had a price ≤ today's price."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Are prices always positive?"
- "Is there a maximum number of calls to `next()`? This affects whether we need to worry about memory."
- "Strictly less than, or less than or equal to? Based on the problem — less than or equal."

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to keep a list of all past prices and, on each call, scan backwards until we find a price greater than today's — O(n) per call, O(n²) total. We can do better with a monotonic stack by compressing old price history into span values."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is a **monotonic decreasing stack of `(price, span)` tuples**. The key insight: instead of storing every past price, I store compressed spans. When a new price arrives and pops stack entries, I accumulate their spans into today's span for free — no backward scanning needed. This gives O(1) amortized per call."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(1) amortized per call and O(n) space total — each price is pushed and popped at most once across all calls."

### Step 6 — Code with Narration (~5–8 minutes)
> "Stack stores tuples of (price, span). I initialize it in __init__..."
> "In next(), I start with span=1 for today itself..."
> "While the stack is non-empty and the top price is ≤ current price, I pop it and add its span to today's span — I'm absorbing all those days into today's streak..."
> "Then I push (price, span) and return span..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "price=100: span=1, stack=[(100,1)], return 1. price=80: 80≤100? No. Stack=[(100,1),(80,1)], return 1. price=60: similar, return 1. price=70: 60≤70, pop(60,1), span=2; 80≤70? No. Return 2. price=75: pop(70,2)→span=3; pop(60,1)→span=4; 80≤75? No. Return 4. price=85: pop(75,4)→span=5; pop(80,1)→span=6; 100≤85? No. Return 6."

### Step 8 — Handle Edge Cases (~1 minute)
> "Strictly increasing prices: each new price pops all previous entries. Span grows as 1, 2, 3, 4... Strictly decreasing: nothing is ever popped, each span is 1. Equal prices: the `<=` condition pops them, so today's span correctly counts the equal-price days."

---

## Mental Model / Intuition

Think of the stack as a **compressed price history**. Instead of keeping every individual day's price, you keep only the "barrier" prices — days that are higher than everything after them.

When today's price arrives, all lower-or-equal prices in the stack become irrelevant as barriers — they'd all be included in today's span. By storing the *span* they represented, you can add it to today's count instantly, without looking at each individual day again.

This is the core amortized trick: you pay the cost of popping when you accumulate the span, but each price is paid for exactly once. Future calls get the span "for free" from the stored tuple.

---

## Approach

1. `__init__`: initialize `self.stack = []` to store `(price, span)` tuples.
2. `next(price)`:
   - Start with `span = 1`
   - While stack is non-empty and `stack[-1][0] <= price`:
     - Pop `(p, s)` and add `s` to `span`
   - Push `(price, span)` onto the stack
   - Return `span`

---

## Code

```python
class StockSpanner:
    def __init__(self):
        self.stack = []  # (price, span)

    def next(self, price):
        span = 1
        while self.stack and self.stack[-1][0] <= price:
            span += self.stack.pop()[1]
        self.stack.append((price, span))
        return span
```

---

## Step-by-Step Walkthrough

Input prices: `[100, 80, 60, 70, 60, 75, 85]`

| price | Pops | span | Stack after | Return |
|-------|------|------|-------------|--------|
| 100 | — | 1 | [(100,1)] | 1 |
| 80 | — | 1 | [(100,1),(80,1)] | 1 |
| 60 | — | 1 | [(100,1),(80,1),(60,1)] | 1 |
| 70 | (60,1) | 2 | [(100,1),(80,1),(70,2)] | 2 |
| 60 | — | 1 | [(100,1),(80,1),(70,2),(60,1)] | 1 |
| 75 | (60,1),(70,2) | 4 | [(100,1),(80,1),(75,4)] | 4 |
| 85 | (75,4),(80,1) | 6 | [(100,1),(85,6)] | 6 |

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(1) amortized | Each price pushed and popped at most once |
| **Space** | O(n) | Stack holds at most n entries across all calls |

---

## Follow-Up Questions

**Q: What does "amortized O(1)" actually mean here?**
It means the *average* cost per call is O(1) over many calls. A single call might pop many entries (O(k)), but those entries were each pushed exactly once and can only be popped once. Total pops across all n calls ≤ n, so average is O(1).

**Q: Why store `(price, span)` instead of just `(price, index)`?**
Storing the index would require `today_index - stored_index` to compute the span — but only if no entries between them were popped. With popped entries, the index math breaks. The `span` field accumulates all the "absorbed" days, so it stays correct regardless of what gets popped.

**Q: What if prices are always strictly increasing?**
Every new price pops all previous stack entries, accumulating the full history into one span. The stack always has exactly one entry after each call. O(n) total work, O(1) space in the stack at any time.
