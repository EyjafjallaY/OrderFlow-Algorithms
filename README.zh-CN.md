# OrderFlow Algorithms

[English](README.md) | **简体中文**

这是一个小型 Python 算法作品集，涵盖动态订单计数、截止时间约束下的排程，以及网格中的最低成本寻路。示例采用演示用输入，并使用 Python 标准库运行。

## 阅读入口

打开 [algorithms.ipynb](algorithms.ipynb)，查看算法实现及已保存的示例输出。阅读 [algorithm-notes.md](docs/algorithm-notes.md)，了解输入假设、正确性论证、复杂度与已知局限。

| 内容 | 实现 | 核心思路 |
| --- | --- | --- |
| 动态订单跟踪 | ProductTracker | 使用字典保存当前计数，并通过带延迟清理机制的堆支持最大积压查询。 |
| 单位时长排程 | min_penalty; min_penalty_greedy | 比较截止时间和逾期罚款约束下的动态规划与贪心选择。 |
| 网格配送成本 | min_delivery_time; min_delivery_time_01 | 根据允许的格子成本，选择 Dijkstra 或 0-1 BFS。 |

排程函数返回最小罚款。网格函数返回包含起始格子成本的最小总成本。这些函数不生成配送排程，也不重建配送路线。

## 运行示例

所保留的代码已在 Python 3.12.14 环境中核验，仅导入 heapq、typing 和 collections.deque。

如果已有兼容 Jupyter 的 Notebook 环境，请选择 Python 3 内核，重启内核后从上到下运行所有单元。打开 Notebook 界面需要相应的 Notebook 环境；算法本身不需要第三方库。

也可以在当前目录下，直接使用普通 Python 执行代码单元：

    python -c "import json; nb=json.load(open('algorithms.ipynb', encoding='utf-8')); scope={}; [exec(''.join(c['source']), scope) for c in nb['cells'] if c['cell_type']=='code']"

预期输出：

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

## 审查重点

- 更新和查询操作是否保持订单跟踪器的当前计数语义。
- 为什么单位时长排程既可以使用精确动态规划求解，也可以使用最优贪心方法求解。
- 为什么二值格子成本允许使用基于双端队列的最短路算法。
- 输入假设和辅助数据结构如何影响正确性与复杂度。

对于 n 个单位时长任务，当最大截止时间为正且 D = min(n, 最大截止时间) 时，动态规划的时间复杂度为 O(n log n + nD)，最坏情况为 O(n²)。贪心实现的时间复杂度为 O(n log n)。对于包含 V 个格子的网格，基于堆的 Dijkstra 时间复杂度为 O(V log V)，0-1 BFS 则为 O(V)。这里列出的是理论复杂度，并非实测加速比。

## 核验与局限

八个保留的代码单元已在全新的 Python 命名空间中顺序执行，其打印输出与原示例保存的结果一致。Notebook 中已有的断言也全部通过。这次整理核验并非自动化测试套件，且尚未在整理环境中核验通过 Jupyter 内核运行的情况。

重要局限已记录在 [algorithm-notes.md](docs/algorithm-notes.md) 中：堆中的历史记录可能积累；部分保留的复杂度注释需要补充限定条件；网格形状和权重假设也没有通过完整的输入校验加以强制保证。

## 来源与整理

本版由一个课程算法项目筛选整理而来。五项实现及其已有演示代码被保留，代码单元文本未作修改。解释性文字和 Notebook 元数据经过重新组织，以便公开审阅；带有身份信息的提交材料未被纳入。

原项目说明了在语言表达、命名和注释方面使用 AI 辅助。本版的文档撰写、内容筛选、元数据清理和核验也使用了 AI 辅助。

## 许可证

本项目采用 [MIT 许可证](LICENSE)。
