---
description: peregrine/peregrinearb/bellmannx.py/
---

# bellmannx.py🚩

```python
import math
import networkx as nx
from .utils import last_index_in_list, load_exchange_graph
from .utils.logging_utils import FormatForLogAdapter
import logging
__all__ = [
    'NegativeWeightFinder',
    'NegativeWeightDepthFinder',
    'bellman_ford',
    'find_opportunities_on_exchange',
    'calculate_profit_ratio_for_path',
    'get_starting_volume',
]


adapter = FormatForLogAdapter(logging.getLogger('peregrinearb.bellmannx'))


class NegativeWeightFinder:
    __slots__ = ['graph', 'predecessor_to', 'distance_to', 'seen_nodes']

    def __init__(self, graph: nx.Graph):
        self.graph = graph
        self.predecessor_to = {}
        # the maximum weight which can be transferred from source to each node
        self.distance_to = {}

        self.seen_nodes = set()

    def reset_all_but_graph(self):
        """
        Call this to look for opportunities after updating the graph
        """
        self.predecessor_to = {}
        self.distance_to = {}

        self.seen_nodes = set()

    def initialize(self, source):
        for node in self.graph:
            # Initialize all distance_to values to infinity and all predecessor_to values to None
            self.distance_to[node] = float('Inf')
            self.predecessor_to[node] = None

        # The distance from any node to (itself) == 0
        self.distance_to[source] = 0

    def bellman_ford(self, source='BTC', unique_paths=True):
        """
        Finds arbitrage opportunities in self.graph and yields them
        Parameters
        ----------
        source
            A node (currency) in self.graph. Opportunities will be yielded only if they are "reachable" from source.
            Reachable means that a series of trades can be executed to buy one of the currencies in the opportunity.
            For the most part, it does not matter what the value of source is, because typically any currency can be
            reached from any other via only a few trades.
        unique_paths : bool
            unique_paths: If True, each opportunity is not yielded more than once
        :return: a generator of profitable (negatively-weighted) arbitrage paths in self.graph
        """
        adapter.info('Running bellman_ford')
        self.initialize(source)

        adapter.debug('Relaxing edges')
        # After len(graph) - 1 passes, algorithm is complete.
        for i in range(len(self.graph) - 1):
            # for each node in the graph, test if the distance to each of its siblings is shorter by going from
            # source->base_currency + base_currency->quote_currency
            for edge in self.graph.edges(data=True):
                self.relax(edge)
        adapter.debug('Finished relaxing edges')

        for edge in self.graph.edges(data=True):
            if self.distance_to[edge[0]] + edge[2]['weight'] < self.distance_to[edge[1]]:
                if unique_paths and edge[1] in self.seen_nodes:
                    continue
                path = self._retrace_negative_cycle(edge[1], unique_paths)
                if path is None or path == (None, None):
                    continue
                yield path

        adapter.info('Ran bellman_ford')

    def relax(self, edge):
        if self.distance_to[edge[0]] + edge[2]['weight'] < self.distance_to[edge[1]]:
            self.distance_to[edge[1]] = self.distance_to[edge[0]] + edge[2]['weight']
            self.predecessor_to[edge[1]] = edge[0]

        return True

    def _retrace_negative_cycle(self, start, unique_paths):
        """
        Retraces an arbitrage opportunity (negative cycle) which a currency can reach and returns it.
        Parameters
        ----------
        start
            A node (currency) from which it is known an arbitrage opportunity is reachable
        unique_paths : bool
            unique_paths: If True, no duplicate opportunities are returned
        Returns
        -------
        list
            An arbitrage opportunity reachable from start. Value is None if seen_nodes is True and a
            duplicate opportunity would be returned.
        """
        arbitrage_loop = [start]
        prior_node = start
        while True:
            prior_node = self.predecessor_to[prior_node]
            # if negative cycle is complete
            if prior_node in arbitrage_loop:
                arbitrage_loop = arbitrage_loop[:last_index_in_list(arbitrage_loop, prior_node) + 1]
                arbitrage_loop.insert(0, prior_node)
                return arbitrage_loop

            # because if prior_node is in arbitrage_loop prior_node must be in self.seen_nodes. thus, this conditional
            # must proceed checking if prior_node is in arbitrage_loop
            if unique_paths and prior_node in self.seen_nodes:
                return None

            arbitrage_loop.insert(0, prior_node)
            self.seen_nodes.add(prior_node)


class NegativeWeightDepthFinder(NegativeWeightFinder):

    def _retrace_negative_cycle(self, start, unique_paths):
        """
        Retraces an arbitrage opportunity (negative cycle) which a currency can reach and calculates the
        maximum amount of the first currency in the arbitrage opportunity that can be used to execute the opportunity.
        Parameters
        ----------
        start
            A node (currency) from which it is known an arbitrage opportunity is reachable
        unique_paths : bool`
            unique_paths: If True, no duplicate opportunities are returned
        Returns
        -------
        2-tuple
            [0] : list
                An arbitrage opportunity reachable from start. Value is None if seen_nodes is True and a
                duplicate opportunity would be returned.
            [1] : float
                The maximum amount of the first currency in the arbitrage opportunity that can be used to execute
                the opportunity. Value is None if seen_nodes is True and a duplicate opportunity would be returned.
        """
        arbitrage_loop = [start]
        prior_node = self.predecessor_to[arbitrage_loop[0]]
        # the minimum weight which can be transferred without being limited by edge depths
        minimum = self.graph[prior_node][arbitrage_loop[0]]['depth']
        arbitrage_loop.insert(0, prior_node)
        while True:
            if arbitrage_loop[0] in self.seen_nodes and unique_paths:
                return None, None
            self.seen_nodes.add(prior_node)

            prior_node = self.predecessor_to[arbitrage_loop[0]]
            edge_weight = self.graph[prior_node][arbitrage_loop[0]]['weight']
            edge_depth = self.graph[prior_node][arbitrage_loop[0]]['depth']
            # if minimum is the limiting volume
            if edge_weight + edge_depth < minimum:
                minimum = max(minimum - edge_weight, edge_depth)
            # if edge_depth is the limiting volume
            elif edge_weight + edge_depth > minimum:
                minimum = edge_depth

            if prior_node in arbitrage_loop:
                arbitrage_loop = arbitrage_loop[:last_index_in_list(arbitrage_loop, prior_node) + 1]
                arbitrage_loop.insert(0, prior_node)
                adapter.info('Retraced loop')
                return arbitrage_loop, math.exp(-minimum)

            arbitrage_loop.insert(0, prior_node)


def bellman_ford(graph, source='BTC', unique_paths=True, depth=False):
    """
    查看NegativeWeightFinder类中bellman_ford方法的文档字符串。 （这是一个静态包装器函数)
    如果depth为true，则从start_amount的权重开始产生所有负加权路径（占深度）。
    """
    if depth:
        return NegativeWeightDepthFinder(graph).bellman_ford(source, unique_paths)
    else:
        return NegativeWeightFinder(graph).bellman_ford(source, unique_paths)


async def find_opportunities_on_exchange(exchange_name, source='BTC', unique_paths=True, depth=False):
    graph = await load_exchange_graph(exchange_name, source, unique_paths, depth)
    return bellman_ford(graph, source, unique_paths, depth)


def get_starting_volume(graph, path):
    adapter.info('Gathering path data', path=str(path))

    volume_scalar = 1
    start = path[0]
    end = path[1]
    initial_volume = math.exp(-graph[start][end]['depth'])
    # 可以以最佳价格交易结束的起始数量，该数量受先前的市场数量限制
    # 在机会中
    previous_volume = initial_volume * math.exp(-graph[start][end]['weight'])
    for i in range(1, len(path) - 1):
        start = path[i]
        end = path[i + 1]
        # 以最佳价格可以交易结束的开始数量
        current_max_volume = math.exp(-graph[start][end]['depth'])
        if previous_volume > current_max_volume:
            volume_scalar *= current_max_volume / previous_volume
            # volume_scalar = min(current_max_volume / previous_volume, volume_scalar)
            previous_volume = current_max_volume
        previous_volume *= math.exp(-graph[start][end]['weight'])
    return initial_volume * volume_scalar


def calculate_profit_ratio_for_path(graph, path, depth=False, starting_amount=1, gather_path_data=False):
    """
    如果collect_path_data，则返回一个二元组，其中第一个元素是给定路径的利润率，而
    第二个元素是由市场符号键控的dict，并由带有“ rate”和“ volume”键的dict赋值， 
    对应于交易的速率和最大交易量。
    数量和汇率始终以基础货币计。
    """
    adapter.info('Calculating profit ratio')
    if gather_path_data:
        path_data = []

    ratio = starting_amount
    for i in range(len(path) - 1):
        start = path[i]
        end = path[i + 1]
        if depth:
            # volume 和 rate_with_fee 是在最开始的角度而言 ，可以是基准货币或报价货币。
            rate_with_fee = math.exp(-graph[start][end]['weight'])
            volume = min(ratio, math.exp(-graph[start][end]['depth']))
            ratio = volume * rate_with_fee

            if gather_path_data:
                sell = graph[start][end]['trade_type'] == 'SELL'
                # 对于买单，以基本货币表示交易量。
                if not sell:
                    volume /= graph[start][end]['no_fee_rate']

                path_data.append({'market_name': graph[start][end]['market_name'],
                                  'rate': graph[start][end]['no_fee_rate'],
                                  'fee': graph[start][end]['fee'],
                                  'volume': volume,
                                  # 待办事项：更改订单及其用法以进行输入
                                  # 如果起点在路径终点之前，则为卖单。
                                  'order': 'SELL' if sell else 'BUY'})
        else:
            ratio *= math.exp(-graph[start][end]['weight'])

    adapter.info('Calculated profit ratio')

    if gather_path_data:
        return (ratio / starting_amount), path_data
    return ratio / starting_amount
```

