# OrderFlow Algorithms

**English** | [简体中文](README.zh-CN.md)

A small Python algorithm portfolio covering dynamic order counts, deadline-constrained scheduling, and minimum-cost grid traversal. The examples use illustrative inputs and run with the Python standard library.

## Start here

Open [algorithms.ipynb](algorithms.ipynb) to review the implementations and saved example outputs. Read [algorithm-notes.md](docs/algorithm-notes.md) for assumptions, correctness arguments, complexity, and known limitations.

| Area | Implementations | Main idea |
| --- | --- | --- |
| Dynamic order tracking | ProductTracker | A dictionary stores current counts; a heap with lazy deletion supports maximum-backlog queries. |
| Unit-time scheduling | min_penalty; min_penalty_greedy | Compare dynamic programming and greedy selection under deadline and late-penalty constraints. |
| Grid delivery costs | min_delivery_time; min_delivery_time_01 | Choose Dijkstra or 0-1 BFS according to the permitted cell costs. |

The scheduling functions return the minimum penalty. The grid functions return the minimum cost, including the starting cell. They do not construct a delivery schedule or reconstruct a route.

## Run the examples

The retained code was checked with Python 3.12.14. It imports only heapq, typing, and collections.deque.

With an existing Jupyter-compatible notebook environment, select a Python 3 kernel and restart it before running all cells from top to bottom. Opening the notebook interface requires a notebook environment; the algorithms themselves require no third-party libraries.

You can also execute the code cells from this directory with plain Python:

    python -c "import json; nb=json.load(open('algorithms.ipynb', encoding='utf-8')); scope={}; [exec(''.join(c['source']), scope) for c in nb['cells'] if c['cell_type']=='code']"

Expected output:

    After adding: top = 202
    After processing 202: top = 303
    After boosting 101: top = 101
    get_pending(101) = 30
    get_pending(202) = 0
    All assertions passed.
    30
    30
    20
    1

## What to review

- Whether updates and queries preserve the order tracker's current-count semantics.
- Why unit-time scheduling admits both an exact DP and an optimal greedy solution.
- Why binary cell costs permit a deque-based shortest-path algorithm.
- How input assumptions and auxiliary data structures affect correctness and complexity.

For n unit-time jobs with a positive maximum deadline and D = min(n, largest deadline), the DP takes O(n log n + nD) time; its worst case is O(n²). The greedy implementation takes O(n log n) time. For a grid with V cells, heap-based Dijkstra takes O(V log V), while 0-1 BFS takes O(V). These are theoretical bounds, not measured speedups.

## Verification and limitations

The eight retained code cells were executed sequentially in a fresh Python namespace, and their printed outputs matched the saved source examples. Existing notebook assertions also passed. This preparation check is not an automated test suite, and execution through a Jupyter kernel has not been checked in this preparation environment.

Important limitations are documented in [algorithm-notes.md](docs/algorithm-notes.md): heap history can accumulate, some retained complexity comments need qualification, and grid shape and weight assumptions are not enforced by complete input validation.

## Origin and preparation

This edition was curated from an academic algorithms project. Five implementations and their existing demonstration code were retained without edits to the code-cell text. Explanatory prose and notebook metadata were reorganized for public review; identifying submission material was omitted.

The original project disclosed AI assistance with language, naming, and comments. AI assistance was also used for this edition's documentation, content selection, metadata cleanup, and verification.

## License

This project is licensed under the [MIT License](LICENSE).
