# LeetCode Interactive Visualizers

Step-by-step animated visualizers for LeetCode problems, built with a dark GitHub theme. Each problem has an **interactive HTML visualizer** and a **README with full interview strategy**.

Live site: [mayobami.github.io/linkedlist-contd](https://mayobami.github.io/linkedlist-contd)

---

## Stacks

| # | Problem | Difficulty | Visualizer | Interview README |
|---|---------|------------|------------|-----------------|
| 71 | Simplify Path | Medium | [View](stacks/Lc71_simplify_path.html) | [Read](stacks/explanations/LC71_Simplify_Path.md) |
| 150 | Evaluate Reverse Polish Notation | Medium | [View](stacks/Lc150_evaluate_reverse_polish_notation.html) | [Read](stacks/explanations/LC150_Evaluate_Reverse_Polish_Notation.md) |
| 155 | Min Stack | Easy | [View](stacks/Lc155_min_stack.html) | [Read](stacks/explanations/LC155_Min_Stack.md) |
| 22 | Generate Parentheses | Medium | [View](stacks/Lc22_generate_parentheses.html) | [Read](stacks/explanations/LC22_Generate_Parentheses.md) |
| 84 | Largest Rectangle in Histogram | Hard | [View](stacks/Lc84_largest_rectangle.html) | [Read](stacks/explanations/LC84_Largest_Rectangle_in_Histogram.md) |
| 402 | Remove K Digits | Medium | [View](stacks/Lc402_remove_k_digits.html) | [Read](stacks/explanations/LC402_Remove_K_Digits.md) |
| 735 | Asteroid Collision | Medium | [View](stacks/Lc735_asteroid_collision.html) | [Read](stacks/explanations/LC735_Asteroid_Collision.md) |
| 739 | Daily Temperatures | Medium | [View](stacks/Lc739_daily_temperatures.html) | [Read](stacks/explanations/LC739_Daily_Temperatures.md) |
| 853 | Car Fleet | Medium | [View](stacks/Lc853_car_fleet.html) | [Read](stacks/explanations/LC853_Car_Fleet.md) |
| 901 | Online Stock Span | Medium | [View](stacks/Lc901_online_stock_span.html) | [Read](stacks/explanations/LC901_Online_Stock_Span.md) |
| 1856 | Maximum Subarray Min-Product | Hard | [View](stacks/Lc1856_maximum_subarray_min_product.html) | [Read](stacks/explanations/LC1856_Maximum_Subarray_Min_Product.md) |

---

## Linked Lists

| # | Problem | Difficulty | Visualizer | README |
|---|---------|------------|------------|--------|
| 21 | Merge Two Sorted Lists | Easy | [View](artifacts/LC21.html) | — |
| 92 | Reverse Linked List II | Medium | [View](artifacts/Lc92_reverse_linked_list_ii.html) | [Read](LC92_Reverse_Linked_List_II_Readme.md) |
| 148 | Sort List | Medium | [View](artifacts/Lc148_sort_list.html) | [Read](LC148_Sort_List_Readme.md) |

---

## Doubly Linked Lists

| # | Problem | Difficulty | README |
|---|---------|------------|--------|
| 2 | Add Two Numbers | Medium | [Read](Doubly%20linked/LC2_Add_Two_Numbers_DLL_Readme.md) |
| 21 | Merge Two Sorted Lists (DLL) | Easy | [Read](Doubly%20linked/LC21_Merge_Two_Lists_DLL_Readme.md) |
| 23 | Merge K Sorted Lists | Hard | [Read](Doubly%20linked/LC23_Merge_K_Lists_DLL_Readme.md) |
| 92 | Reverse Linked List II (DLL) | Medium | [Read](Doubly%20linked/LC92_Reverse_Between_DLL_Readme.md) |
| 143 | Reorder List | Medium | [Read](Doubly%20linked/LC143_Reorder_List_DLL_Readme.md) |
| 203 | Remove Linked List Elements | Easy | [Read](Doubly%20linked/LC203_Remove_Elements_DLL_Readme.md) |
| 234 | Palindrome Linked List | Easy | [Read](Doubly%20linked/LC234_Palindrome_DLL_Readme.md) |

---

## How to Use the Visualizers

1. Open any `.html` file in a browser (no server required — pure HTML/JS/CSS).
2. Click **Next →** or press the **→ arrow key** to step forward through the algorithm.
3. The left panel shows the data structure state; the right panel shows highlighted code and variable values.
4. The footer explains what changed and why at each step.

---

## Repository Structure

```
linkedlist-contd/
├── README.md                        ← this file
├── CLAUDE.md                        ← AI instructions + design system
├── artifacts/                       ← linked-list HTML visualizers
├── Doubly linked/                   ← DLL visualizers + READMEs
└── stacks/
    ├── Lc{number}_{name}.html       ← stack HTML visualizers
    └── explanations/
        └── LC{number}_{Name}.md     ← interview strategy READMEs
```
