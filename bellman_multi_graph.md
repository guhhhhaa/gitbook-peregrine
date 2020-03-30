---
description: peregrine/peregrinearb/bellman_multi_graph.py/
---

# bellman\_multi\_graph.py

```python
import networkx as nx
from .bellmannx import NegativeWeightFinder
from .utils import get_least_edge_in_bunch
__all__ = [
    'NegativeWeightFinderMulti',
    'bellman_ford_multi',
]


class NegativeWeightFinderMulti(NegativeWeightFinder):

    def __init__(self, graph: nx.MultiGraph):
        super(NegativeWeightFinderMulti, self).__init__(graph)
        self.new_graph = nx.DiGraph()

    def bellman_ford(self, source='BTC', unique_paths=True):
        self.initialize(source)

        # 第一次迭代时，加载市场价格。
        self._first_iteration()

        # len（graph）-1 pass 之后，算法完成。
        for i in range(1, len(self.graph) - 1):
            for edge in self.new_graph.edges(data=True):
                self.relax(edge)

        for edge in self.new_graph.edges(data=True):
            # 待办事项：这是否表明存在一个以edge [1]开始和结束的负循环？还是那样
            # edge [1]连接到负周期?
            if self.distance_to[edge[0]] + edge[2]['weight'] < self.distance_to[edge[1]]:
                path = yield self._retrace_negative_cycle(edge[1], unique_paths=unique_paths)
                if path is None or path is (None, None):
                    continue
                yield path

    def _first_iteration(self):
        """
        在第一次迭代中，找到self.graph中每个边缘束之间的最小权重边缘，并创建一个有向图，
        self.new_graph使用那些权重最小的边。同时完成第一次松弛迭代。
        这就是为什么在bellman_ford中只有len（self.graph）-1次迭代以放松边缘。
        （第一次迭代已在该方法中完成。）
        """
        [self._process_edge_bunch(edge_bunch) for edge_bunch in self.graph.edge_bunches(data=True)]

    def _process_edge_bunch(self, edge_bunch):
        ideal_edge = get_least_edge_in_bunch(edge_bunch)
        # 待办事项：这会发生吗？如果是这样，edge_bunch中权重最小的边必须具有无限的权重
        if ideal_edge['weight'] == float('Inf'):
            return

        self.new_graph.add_edge(edge_bunch[0], edge_bunch[1], **ideal_edge)

        # 待办事项：这些条件很少都成立。如何确定何时是这种情况？
        if self.distance_to[edge_bunch[0]] + ideal_edge['weight'] < self.distance_to[edge_bunch[1]]:
            self.distance_to[edge_bunch[1]] = self.distance_to[edge_bunch[0]] + ideal_edge['weight']
            self.predecessor_to[edge_bunch[1]] = edge_bunch[0]


def bellman_ford_multi(graph: nx.MultiGraph, source, unique_paths=True):
    """
    返回一个2元组，其中包含在每个边缘束中权重最大的图和一个在图的负周期内迭代的生成器
    """
    finder = NegativeWeightFinderMulti(graph)
    paths = finder.bellman_ford(source, unique_paths)
    return finder.new_graph, paths
```

