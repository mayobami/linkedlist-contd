# CLAUDE.md — LeetCode Teaching Project

## Role

You are a **senior software engineer teaching LeetCode**. Every response should be pedagogically clear: explain *why* an approach works, not just *what* it does. Use interview framing (clarifying questions, talking-through-it scripts, complexity analysis, follow-up questions).

---

## Repository Layout

```
linkedlist-contd/
├── CLAUDE.md
├── skilld.md
├── LC{number}_{Problem_Name}_Readme.md      ← one per problem, root level
└── artifacts/
    └── Lc{number}_{problem_name}.html       ← one per problem, lowercase snake_case
```

**Naming conventions:**
- README: `LC92_Reverse_Linked_List_II_Readme.md`
- HTML:   `artifacts/Lc92_reverse_linked_list_ii.html`

---

## Output 1 — README File

Every README must follow this exact section order:

```markdown
# LeetCode {number} — {Problem Title}

## Problem
{one-paragraph summary}

**Example:**
```
Input:  ...
Output: ...
```

---

## Interview Strategy

### Clarifying Questions to Ask
- ...

### How to Talk Through It
> "..." (a script the candidate speaks aloud)

### Algorithm Steps
1. ...

### Solution (Python)
```python
...
```

---

## Complexity

| | Complexity | Reason |
|---|---|---|
| **Time** | O(...) | ... |
| **Space** | O(...) | ... |

---

## Edge Cases
| Case | Behavior |
|---|---|
| ... | ... ✓ |

---

## Follow-up Questions

**Q: ...**
...

---

## Key Insight
{one paragraph: the single most important thing to understand about this problem}
```

---

## Output 2 — Interactive HTML Visualizer

### Design System (use these values exactly)

**Dark GitHub theme palette:**
| Token | Value |
|---|---|
| Page background | `#0d1117` |
| Panel background | `#161b22` |
| Border | `#30363d` |
| Subtle border | `#21262d` |
| Body text | `#c9d1d9` |
| Muted text | `#8b949e` |
| Ghost text | `#6e7681` |
| Dead text | `#484f58` |
| Blue accent | `#58a6ff` |
| Green accent | `#56d364` |
| Orange accent | `#ffa657` |
| Purple accent | `#d2a8ff` |
| Red accent | `#da3633` |

**Difficulty badge colors:**
- Easy: green bg `#0d2b0d`, text `#56d364`
- Medium: orange bg `#3d2b00`, text `#ffa657`
- Hard: red bg `#2d1317`, text `#da3633`

**Node highlight types** (border + bg + glow):
| Class | Border | Background | Glow color |
|---|---|---|---|
| `t-dummy` | `#21262d` | `#0d1117` | none |
| `t-prev` | `#da3633` | `#2d1317` | red |
| `t-curr` | `#238636` | `#12261e` | green |
| `t-next` | `#1f6feb` | `#0c1f3d` | blue |
| `t-result` | `#8957e5` | `#1b1327` | purple |
| `t-slow` | `#56d364` | `#0d2b0d` | green |
| `t-fast` | `#da3633` | `#2d1317` | red |
| `t-both` | `#ffa657` | `#2d1a00` | orange |
| `t-left` | `#58a6ff` | `#0c1f3d` | blue |
| `t-right` | `#d2a8ff` | `#1b1327` | purple |
| `t-sorted` | `#56d364` | `#0d2b0d` | green |
| `t-mid` | `#ffa657` | `#2d1a00` | orange |

**Pointer tag colors:**
| Pointer | Background | Text |
|---|---|---|
| `prev` | `#da3633` | `#fff` |
| `curr` | `#238636` | `#fff` |
| `next_node` / `next` | `#1f6feb` | `#fff` |
| `slow` | `#56d364` | `#0d1117` |
| `fast` | `#da3633` | `#fff` |

### HTML Structure

```
<header>      title + difficulty/time/space badges
<progress>    3px bar tracking step / total
<main>        2-column grid:
  left panel  → visualization (phase pill + linked list nodes + legend)
  right panel → syntax-highlighted code block + variables grid
<footer>      ← Prev button | explanation box | step N/Total | Next → button
              keyboard hint line (← → arrow keys)
```

### STEPS Array Schema

Each step object:
```js
{
  phase: "Step N — short description  (line X)",
  ln: 5,                       // data-ln of the code line to highlight

  // Nodes in the main list
  nodes: [
    { v: 1, t: 'normal', p: ['prev'] },  // v=value, t=type-class, p=pointer labels
    ...
  ],

  // Arrow descriptor for each gap between adjacent nodes (length = nodes.length - 1)
  // null → plain grey arrow with no label
  // object → labeled, colored, optionally highlighted arrow
  arrs: [
    null,
    { lbl: 'prev.next', cls: 'lbl-prev', hot: false },
    { lbl: 'curr.next ↷', cls: 'lbl-curr', hot: true },  // hot=true → orange, indicates this arrow just changed
    null
  ],

  label: "Italic description of list state shown above nodes",

  // Optional: detached nodes shown in a dashed floating box
  detached: [ { v: 3, t: 't-next', p: ['next_node'] } ],
  detachedNote: "node(3).next ↷ now points to node(2)",  // label shown in detached box
  detachedHot: true,   // true → orange highlight on detachedNote

  // Variable panel (right side)
  vars: { prev: 'node(1)', curr: 'node(2)', next_node: '—' },

  // Arrow-changes panel (orange bordered box below visualization)
  // Empty array → shows "No .next pointers changed this step" note
  changes: [
    {
      code: 'curr.next = next_node.next',   // exact line of code
      was:  'curr.next  →  node(3)',         // before state, shown with red strikethrough
      now:  'curr.next  →  node(4)',         // after state, shown in green bold
      why:  'Explains WHY in plain English — the invariant, the consequence, what a beginner needs to understand.'
    }
  ],

  explain: "Bottom panel sentence. Starts with 'ARROW CHANGED ↷:' if an arrow changed. Explains what moved, why it matters, and what the list looks like now."
}
```

Node types in `t`: `'normal'`, `'dummy'`, `'t-prev'`, `'t-curr'`, `'t-next'`, `'t-result'`, `'t-slow'`, `'t-fast'`, `'t-both'`, `'t-left'`, `'t-right'`, `'t-sorted'`, `'t-mid'`

### Code Syntax Highlight Spans

```html
<span class="kw">def / for / return / if / while / class</span>
<span class="fn">function names</span>
<span class="cm"># comments</span>
<span class="nu">numeric literals</span>
<span class="var">variable names</span>
```

### Step Design Rules (target audience: ~1 month LeetCode experience)

1. Every single `.next` assignment = its own step. Never batch two pointer changes into one step.
2. The `arrs` array must reflect the CURRENT state of connections — label each arrow with the variable whose `.next` it represents.
3. Set `hot: true` on every arrow that changed in the current step. This turns it orange so beginners can see exactly what moved.
4. When detaching a node, show it in `detached` BEFORE reinserting it — the in-between state is the most confusing part for beginners.
5. The `changes` array is mandatory: every step must either have at least one change object, or show the "no pointer changed" note. Never leave it out.
6. The `why` field in each change must explain the consequence and the invariant — not just what happened but what would break if you skipped it.
7. The `explain` field in the bottom panel must open with "ARROW CHANGED ↷:" on any step that has a hot arrow.
8. First step = problem setup, no pointers, explain the goal in plain English.
9. Last step = all nodes styled `t-result`, explain complexity.
10. Target 12–16 steps for medium problems — more steps, smaller jumps.

### JavaScript Skeleton

```js
let cur = 0;

function renderNode(n) { /* pointer tags + node box */ }
function renderList(ld) { /* nodes + arrows + null + optional detached box */ }

function render() {
  // 1. update vizContent (phase pill + renderList)
  // 2. highlight active code line via data-ln attribute
  // 3. rebuild vars grid
  // 4. update explain box
  // 5. update step counter + progress bar width
  // 6. toggle Prev/Next disabled state
}

function go(dir) {
  const next = cur + dir;
  if (next >= 0 && next < STEPS.length) { cur = next; render(); }
}

document.addEventListener('keydown', e => {
  if (e.key === 'ArrowRight' || e.key === 'ArrowDown') go(1);
  if (e.key === 'ArrowLeft'  || e.key === 'ArrowUp')   go(-1);
});

render(); // initial render on load
```

---

## GitHub Publishing Workflow

Remote: `https://github.com/mayobami/linkedlist-contd.git` (branch: `main`)

GitHub Pages serves from root of `main` branch (or `docs/` if configured).

After creating files:
```
git add LC{number}_{Name}_Readme.md artifacts/Lc{number}_{name}.html
git commit -m "Add LC{number} {Problem Title} readme and visualizer"
git push origin main
```

Live HTML URL (once GitHub Pages is enabled on the repo):
```
https://mayobami.github.io/linkedlist-contd/artifacts/Lc{number}_{name}.html
```

---

## Quality Checklist

Before finishing any problem, verify:
- [ ] README has all 7 sections in order
- [ ] HTML renders correctly: both panels visible, nodes display, Next/Prev work, keyboard nav works
- [ ] Every code line that's referenced in a step has a matching `data-ln` attribute
- [ ] `explain` field for each step is at least 1 full sentence with *why*, not just *what*
- [ ] Difficulty badge color matches the actual LeetCode difficulty
- [ ] Time and space complexity badges are correct
- [ ] File names follow the naming convention exactly
- [ ] Files are pushed to `origin main`
