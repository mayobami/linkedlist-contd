# LC 71 — Simplify Path

## Problem Summary

Given a Unix-style absolute file path string (starting with `/`), return its **simplified canonical form**. Collapse double slashes, resolve `.` (current dir) and `..` (parent dir) tokens, and strip trailing slashes. The result must always start with `/`.

**Example:**
```
Input:  "/home/../home//foo/./"
Output: "/home/foo"
```

---

## Interview Answer

### Step 1 — Read & Restate (~30 seconds)
Read the problem silently, then say:
> "So the problem is asking me to take a Unix path string and simplify it — collapsing double slashes, resolving dots and double dots — returning the shortest canonical form."

### Step 2 — Ask Clarifying Questions (~1 minute)
> "Before I start, I have a few questions."
- "Can the path have consecutive slashes like `//` or `///`?"
- "Should I handle paths that try to go above root with `..`, like `/../../x`? Presumably they just stay at root."
- "Is the input guaranteed to be a valid absolute path starting with `/`?"

### Step 3 — Think Out Loud with Brute Force First (~1 minute)
> "The brute force would be to repeatedly scan for `..` and `.` tokens using string replacement until none remain — that's messy and O(n²). But if I just split on `/` and process each part once with a stack, I get O(n) and it's much cleaner."

### Step 4 — State the Optimal Approach (~1 minute)
> "My approach is to use a **stack** because navigating a directory path is naturally a LIFO problem — `..` means 'undo the last push'. I'll split the path on `/`, then for each part: skip empty strings and `.`, pop for `..`, and push everything else."

### Step 5 — State Complexity Before Coding (~30 seconds)
> "This will be O(n) time and O(n) space, where n is the length of the path string — we process each character once during the split and each token once during the loop."

### Step 6 — Code with Narration (~5–8 minutes)
> "Here I split the path on `/` to get individual tokens..."
> "This if-elif handles the three special cases: `..` pops, `.` and empty strings are skipped, everything else is pushed..."
> "Finally I join the stack with `/` and prepend the required leading slash..."

### Step 7 — Dry Run Your Code (~2 minutes)
> "Let me trace through `/home/../home//foo/./`. After split: `['', 'home', '..', 'home', '', 'foo', '.', '']`. Empty strings and `.` are skipped. `home` → push. `..` → pop. `home` → push. `foo` → push. Stack is `['home', 'foo']`. Result: `/home/foo`. Correct."

### Step 8 — Handle Edge Cases (~1 minute)
> "If the input is `/`, split gives `['', '']` — all skipped, stack empty, result `/`. If path goes above root like `/../x`, the `..` on empty stack is ignored because of the `if stack:` guard, giving `/x`. Both handled correctly."

---

## Mental Model / Intuition

Think of the stack as your **current directory history**. Every real directory name you `cd` into gets pushed. `cd ..` pops the last one. `.` and empty segments are no-ops. At the end, join what's left — that's your location.

The stack's LIFO property directly mirrors how filesystem navigation works: the most recently visited directory is always the one that `..` undoes.

---

## Approach

1. Split the path string on `/` to get all tokens (including empty strings from double slashes).
2. For each token:
   - Skip if empty or `.`
   - Pop the stack if `..` (and stack is not empty)
   - Push to stack otherwise
3. Rejoin stack with `/` and prepend `/`.

---

## Code

```python
def simplifyPath(self, path: str) -> str:
    stack = []
    for part in path.split('/'):
        if part == '..':
            if stack: stack.pop()
        elif part and part != '.':
            stack.append(part)
    return '/' + '/'.join(stack)
```

---

## Step-by-Step Walkthrough

Input: `/home/../home//foo/./`

| Token | Action | Stack |
|-------|--------|-------|
| `` (empty) | skip | `[]` |
| `home` | push | `['home']` |
| `..` | pop | `[]` |
| `home` | push | `['home']` |
| `` (empty) | skip | `['home']` |
| `foo` | push | `['home', 'foo']` |
| `.` | skip | `['home', 'foo']` |
| `` (empty) | skip | `['home', 'foo']` |

Result: `'/' + 'home/foo'` → `/home/foo`

---

## Complexity Table

| | Complexity | Reason |
|---|---|---|
| **Time** | O(n) | Split is O(n); each token pushed/popped at most once |
| **Space** | O(n) | Stack holds at most n/2 directory names |

---

## Follow-Up Questions

**Q: What if the path contains filenames with spaces?**
Split on `/` handles spaces transparently since spaces aren't the delimiter. A name like `my folder` would be pushed as a single token.

**Q: What if path tokens contain `...` (three dots)?**
Three dots is a valid directory name — not a special token. It gets pushed to the stack as-is.

**Q: Can you do this without `split()`?**
Yes — scan character by character, building tokens manually. Same O(n) complexity but more code. `split('/')` is idiomatic Python.
