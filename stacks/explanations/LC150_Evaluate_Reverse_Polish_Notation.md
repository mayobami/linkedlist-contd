# LC 150 — Evaluate Reverse Polish Notation

## Problem Summary

Evaluate an arithmetic expression in **Reverse Polish Notation (RPN)** — where operators follow their operands. Tokens are integers or one of `+`, `-`, `*`, `/`. Division truncates toward zero.

**Example:**
```
Input:  tokens = ["5","1","2","+","4","*","+","3","-"]
Output: 14
Explanation: ((5 + (1 + 2)) * 4) - 3 = (5 + 3 * 4) - 3 = 20 - 3 = 14
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
> "So the problem is asking me to evaluate a mathematical expression given in postfix (RPN) format — where I process tokens left to right, and whenever I see an operator I apply it to the two most recent operands."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, a few questions."
- "Is division always integer division truncating toward zero — not floor division?"
- "Can there be negative numbers in the input, or just operators that produce negative results?"
- "Is the input guaranteed to be a valid RPN expression? I'll assume yes."

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "There's no real 'brute force' alternative here — RPN is specifically designed for stack evaluation. The stack IS the standard algorithm. Without it, you'd need to parse the expression tree which is far more complex."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach: use a **stack for operands**. Scan tokens left to right. Push numbers onto the stack. When you hit an operator, pop two operands, apply the operator, push the result back. After processing all tokens, the single remaining stack element is the answer. The stack manages the operand pairing automatically."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space — we process each token once, and the stack holds at most n/2 elements at any time."

### Step 6 — Code with Narration (~5–8 minutes)
> "I check if each token is an operator using `tok in '+-*/'`..."
> "CRITICAL: I pop `b` first (it was pushed last — it's the RIGHT operand), then `a` (pushed earlier — LEFT operand). This matters for subtraction and division: a-b not b-a..."
> "For division I use `int(a / b)` not `a // b` — floor division rounds toward negative infinity but we need truncation toward zero. Example: -7//2=-4 but int(-7/2)=-3..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "tokens=['5','1','2','+','4','*','+','3','-']. Push 5,1,2. '+': b=2,a=1, push 3. Stack=[5,3]. '4': push. Stack=[5,3,4]. '*': b=4,a=3, push 12. Stack=[5,12]. '+': b=12,a=5, push 17. Wait let me recheck — 5+(1+2)*4=5+12=17, then 17-3=14. Yes. '-': b=3,a=17, push 17-3=14. Return stack[0]=14."

### Step 8 — Handle Edge Cases (~1 minute)
> "Single token like ['5']: loop pushes 5, no operators, returns stack[0]=5. Negative numbers as strings like ['-3']: `int('-3')=-3`, handled by the else branch correctly. Division truncation: ['7','2','/']=3, ['-7','2','/']=int(-3.5)=-3 (not -4)."

---

## Mental Model / Intuition

RPN eliminates the need for parentheses by placing operators **after** their operands. The stack is a natural match: operands wait on the stack until their operator arrives to consume them.

Think of it as a **loading dock**. Numbers arrive and pile up (push). When an operator truck arrives, it picks up exactly the top two items (LIFO: the most recent two operands), combines them, and drops the result back on the dock (push result). The dock always ends with one item — the final answer.

The subtle catch: pop `b` first (it was pushed more recently = the right operand), then pop `a` (pushed earlier = the left operand). For `a - b` and `a / b`, swapping them gives the wrong answer.

---

## Approach

1. Initialize `stack = []`.
2. For each `tok` in `tokens`:
   - If `tok` is an operator (`+`, `-`, `*`, `/`):
     - `b = stack.pop()` (right operand — pushed last)
     - `a = stack.pop()` (left operand — pushed first)
     - Compute result and `stack.append(result)`
   - Else: `stack.append(int(tok))`
3. Return `stack[0]`.

---

## Code

```python
def evalRPN(self, tokens):
    stack = []
    for tok in tokens:
        if tok in '+-*/':
            b = stack.pop()  # top (pushed last) = right operand
            a = stack.pop()  # below (pushed first) = left operand
            if   tok == '+': stack.append(a + b)
            elif tok == '-': stack.append(a - b)
            elif tok == '*': stack.append(a * b)
            else:            stack.append(int(a / b))
        else:
            stack.append(int(tok))
    return stack[0]
```

---

## Step-by-Step Walkthrough

Input: `["5","1","2","+","4","*","+","3","-"]`

| Token | Action | Stack |
|-------|--------|-------|
| "5" | push 5 | [5] |
| "1" | push 1 | [5,1] |
| "2" | push 2 | [5,1,2] |
| "+" | b=2,a=1, push 3 | [5,3] |
| "4" | push 4 | [5,3,4] |
| "*" | b=4,a=3, push 12 | [5,12] |
| "+" | b=12,a=5, push 17 | [17] |
| "3" | push 3 | [17,3] |
| "-" | b=3,a=17, push 14 | [14] |

Result: `stack[0] = 14`

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Each token processed exactly once |
| **Space** | O(n) | Stack holds at most n/2 operands at any time |

---

## Follow-Up Questions

**Q: Why use `int(a / b)` instead of `a // b` for division?**
Python's `//` (floor division) rounds toward negative infinity: `-7 // 2 = -4`. The problem requires truncation toward zero: `int(-7 / 2) = -3`. For positive numbers they agree, but for negative numbers they differ.

**Q: Why pop `b` before `a`?**
The most recently pushed operand is the RIGHT operand. In the expression `a - b`, `a` was pushed first (deeper in stack), `b` was pushed last (top). Popping `b` first gives us `a - b`; popping `a` first would give `b - a` — wrong for subtraction and division.

**Q: Could the input ever be invalid (e.g., too few operands for an operator)?**
The problem guarantees a valid RPN expression, so the stack will always have at least 2 elements when an operator is encountered. In production code you'd add validation.
