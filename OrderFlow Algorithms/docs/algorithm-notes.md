# Algorithm Notes

These notes describe the behavior of the retained code and clarify its input assumptions. The code cells are unchanged; where their original comments simplify a bound, the qualifications below apply.

## 1. Dynamic order tracking

### Interface and assumptions

ProductTracker provides four operations:

- add_order(product_id, quantity): increase a product's pending count.
- process_order(product_id, quantity): decrease the count, clamping it at zero.
- get_pending(product_id): return the current count, or zero for an absent product.
- top_product(): return an identifier with the greatest positive pending count, or None when none remain.

Use non-negative integer quantities and mutually comparable, hashable product identifiers. The examples use integer identifiers. Negative quantities raise ValueError. With equal counts, heap tuple ordering favors the smaller identifier. The retained implementation does not validate every possible type or identifier combination.

### Design and correctness

The dictionary is the authoritative record of current counts. Each update can push a pair of negative count and identifier into the heap. A query discards a heap record when its count differs from the dictionary's current value or its product has no positive pending orders.

Every positive current count has a corresponding heap record. Once a valid record reaches the heap root, no larger current count can exist: its own current record would have higher heap priority. Thus the returned identifier has a maximum current count. Historical records that happen to match the current count again are harmless for the answer, although they still occupy space.

### Complexity clarification and memory limitation

Let P be the number of currently tracked products, H the number of heap records, and R the number of historical records removed by a particular query.

| Operation | Bound for this retained implementation |
| --- | --- |
| Dictionary count lookup | Expected O(1) |
| Heap push during an update | O(log(H + 1)), in addition to expected O(1) dictionary work |
| top_product with an immediately valid root | O(1) |
| top_product removing R records | O(1 + R log(H + 1)), using H at query entry as an upper bound |
| Auxiliary storage | O(P + H) |

Across a mixed sequence of operations, each inserted record can be removed at most once. This gives an amortized accounting of heap cleanup over the sequence, using the maximum heap size in that sequence. It does not give a worst-case O(log P) guarantee for every individual query.

The retained comments use the product count when stating logarithmic costs. The actual heap size H is the relevant quantity. Many updates, including repeated records for the same product, can make H much larger than P. No automatic heap rebuilding is implemented in this edition.

For infrequent maximum queries, scanning a plain dictionary may be a simpler alternative. More frequent queries make the auxiliary heap useful, subject to its history and memory costs.

## 2. Unit-time deadline scheduling

### Objective and input contract

Each job takes exactly one time unit. A job completed after its integer deadline incurs its non-negative penalty. The functions return the minimum possible total late penalty, rather than a list of scheduled jobs.

Provide deadline and penalty lists of equal length. Empty lists return zero. A deadline at or below zero means the job cannot finish on time when work starts at time zero. Unequal list lengths raise ValueError. Non-negative penalties and integer deadlines are assumptions of the methods; comprehensive type and sign validation is not implemented.

### Dynamic programming: min_penalty

Sort jobs by deadline. For any selected on-time subset, earliest-deadline-first order is sufficient to check feasibility.

For a positive maximum deadline, let D = min(n, maximum deadline). The state dp[t] stores the greatest penalty saved by selecting exactly t on-time jobs among those processed so far. Only dp[0] is initially reachable. A job can be selected into slot t only if t does not exceed its deadline. Updating t downward prevents using the same job twice.

These transitions enumerate rejection and feasible acceptance of each job. Subtracting the greatest saved penalty from the total penalty gives the minimum late penalty.

- Time: O(n log n + nD), including sorting; O(n²) in the worst case.
- DP array: O(D).
- Complete auxiliary space: O(n + D), including the sorted job list.

O(D) describes the DP array alone; the complete implementation also allocates a sorted list.

### Greedy heap selection: min_penalty_greedy

Process jobs in deadline order and keep the penalties of selected on-time jobs in a min-heap. If the selected count exceeds the current deadline, remove the smallest penalty.

For unit-time jobs, feasibility can be expressed by nested deadline-prefix capacity constraints. The feasible subsets have the exchange property that supports optimal weighted greedy selection. Removing the least valuable job when a prefix capacity is exceeded keeps a maximum-value feasible selection. The returned total penalty minus saved penalties is therefore optimal under the stated contract.

- Time: O(n log n), including sorting and heap work.
- Auxiliary space: O(n).

Both retained example implementations return 30 for the same input. Their theoretical complexity differs; this edition contains no wall-clock benchmark. The greedy guarantee relies on unit job durations.

## 3. Minimum-cost grid delivery

### Shared interpretation and assumptions

The grid is a rectangular array of integer cell costs. A move enters an orthogonally adjacent cell and adds that cell's cost. Movement is allowed up, down, left, and right. The starting cell's cost is included. The target is the bottom-right cell.

Both functions return a cost only. They assume all cells are traversable. An empty grid or an empty first row returns zero by the retained convention. Ragged grids and unsupported weights are outside the documented contract; the implementations do not comprehensively reject them.

### General non-negative costs: min_delivery_time

Dijkstra's algorithm uses a min-heap of candidate distances and ignores historical entries already improved upon. Non-negative costs ensure that a valid minimum-distance vertex can be finalized when extracted.

The function exits as soon as the target is extracted. Its returned target cost is final, but internal distances for other cells can still be unsettled or undiscovered. A matrix captured at this point must not be described as a completed all-cells shortest-distance result.

For V = rows × columns, the grid has O(V) edges:

- Time: O(V log V), using a binary heap.
- Auxiliary space: O(V).

The retained example returns 20. A minimum-hop route need not have minimum cost when cell weights differ. Four-directional movement also introduces cycles, so a single right/down DP pass does not solve the general problem.

### Binary costs: min_delivery_time_01

This function requires every cell cost to be either zero or one. It uses a deque: entering a zero-cost cell pushes the neighbor to the front, while entering a one-cost cell pushes it to the back. This maintains the shortest-path processing order without heap maintenance.

- Time: O(V + E), which is O(V) for this grid.
- Auxiliary space: O(V).

The retained binary-grid example returns 1. The linear bound and correctness argument depend on the binary-cost restriction. The function does not reject other weights, so its name and input contract must be respected by callers.

## Scope of this edition

The three areas above form the curated public-review edition. Explanations, metadata, and presentation were cleaned up, while implementation and demonstration code were preserved. Known limitations are documented rather than silently repaired. There is no added test suite, heap-compaction feature, input-validation layer, or performance benchmark in this edition.
