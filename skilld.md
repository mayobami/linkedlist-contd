# skilld.md — Workflow for New LeetCode Problems

Use this as a step-by-step recipe every time you add a new problem to this repo.

---

## Step 1 — Understand the Problem

Before writing anything:
1. State the problem in one sentence
2. Identify the data structure (linked list, tree, graph, array…)
3. Identify the algorithm family (two-pointer, divide & conquer, BFS, DP…)
4. Write out the brute-force approach and its complexity
5. Identify why the optimal approach is better
6. Pick a concrete example: small enough to trace by hand (4–6 nodes)

---

## Step 2 — Write the README

File: `LC{number}_{Problem_Title}_Readme.md` in the repo root.

Fill each section in order:

| Section | What to write |
|---|---|
| **Problem** | One-paragraph summary + input/output example |
| **Clarifying Questions** | 3–4 questions a candidate should ask in an interview |
| **How to Talk Through It** | A first-person script the candidate speaks aloud |
| **Algorithm Steps** | Numbered pseudocode steps (not code, not prose) |
| **Solution (Python)** | Clean, commented Python with type hints |
| **Complexity** | Time + Space table with reasoning |
| **Edge Cases** | Table: case → behavior, mark each ✓ |
| **Follow-up Questions** | 4–6 Q&A pairs an interviewer might ask |
| **Key Insight** | One paragraph: the non-obvious thing that makes this solution work |

---

## Step 3 — Plan the HTML Steps

Before writing any HTML, map out the steps on paper or in a comment:

```
Step 0: Problem setup — raw input, no pointers, no labels on arrows
Step 1: Initialize pointers (dummy, prev, curr…)
Step 2: First meaningful pointer move
...
Step N-1: Final operation
Step N: Result — all result nodes styled t-result
```

Rules:
- Every `.next` assignment = its own step — never combine two pointer changes
- Every node detachment = its own step (show it floating BEFORE reinserting)
- Every loop iteration = 3 steps: save next_node → redirect curr.next → insert with prev.next
- Set `hot: true` on the arrow that changed so it highlights orange
- Write a `changes` entry for EVERY step — even variable-only steps get an empty array with a note
- `arrs` labels must match current live connections — update them each step

Teaching target: someone with ~1 month LeetCode experience. Assume they know what a linked list is but do NOT intuitively know why we save a pointer before cutting a connection.

---

## Step 4 — Write the HTML Visualizer

File: `artifacts/Lc{number}_{problem_name}.html`

Build in this order:
1. Copy the CSS design system from an existing file (Lc92 or Lc148)
2. Write the HTML structure (header → progress → main grid → bottom nav)
3. Write the STEPS array — one object per step, following the schema in CLAUDE.md
4. Write the `renderNode` / `renderList` / `render` / `go` functions
5. Add keyboard event listener
6. Call `render()` at the bottom

Adapt the left panel visualization to the algorithm:
- Linked list problems: show nodes as boxes with arrows → null
- Tree problems: adapt to vertical/horizontal tree layout
- Two-pointer problems: show both pointers on the same list

---

## Step 5 — Test the HTML Locally

Open the HTML file in a browser and verify:
- [ ] Both panels fill the screen (no overflow)
- [ ] First step shows problem setup correctly
- [ ] Next → advances through all steps
- [ ] ← Prev works from any step
- [ ] Keyboard ← → keys work
- [ ] Active code line highlights correctly at each step
- [ ] Variables panel updates at each step
- [ ] Explanation box has meaningful text at every step
- [ ] Last step shows result nodes in purple (`t-result`)
- [ ] Progress bar fills smoothly

---

## Step 6 — Commit and Push

```bash
git add LC{number}_{Name}_Readme.md artifacts/Lc{number}_{name}.html
git commit -m "Add LC{number} {Problem Title} readme and visualizer"
git push origin main
```

GitHub Pages URL (once Pages is enabled):
```
https://mayobami.github.io/linkedlist-contd/artifacts/Lc{number}_{name}.html
```

---

## Step 7 — Enable GitHub Pages (one-time setup)

1. Go to the repo on GitHub: `https://github.com/mayobami/linkedlist-contd`
2. Settings → Pages → Source: **Deploy from a branch**
3. Branch: `main`, Folder: `/ (root)`
4. Save — Pages will be live in ~1 minute

After this is done once, every push to `main` auto-deploys.

---

## Problems Completed

| # | Title | Difficulty | README | HTML | Live |
|---|---|---|---|---|---|
| 92 | Reverse Linked List II | Medium | ✓ | ✓ v2 (arrow-change labels) | pending Pages setup |
| 148 | Sort List | Medium | ✓ | ✓ (needs v2 upgrade) | pending Pages setup |

Add a row here each time you complete a new problem.

---

## Cheat Sheet — Common Algorithm Patterns

| Pattern | Key Pointers | Loop runs | Use when |
|---|---|---|---|
| Reverse sublist (insert-to-front) | dummy, prev, curr, next_node | right − left | LC92, LC25 |
| Find middle (slow/fast) | slow, fast | until fast.next null | LC148, LC876 |
| Merge two sorted lists | dummy, curr, left, right | until one exhausted | LC21, LC148 |
| Detect cycle | slow, fast | until they meet or fast null | LC141, LC142 |
| Remove nth from end | dummy, fast, slow | n steps ahead, then together | LC19 |
| Reorder list | slow/fast find mid, reverse second half, merge | — | LC143 |
