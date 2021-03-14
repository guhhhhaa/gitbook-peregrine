# 开发目标🚩

peregrine只是打印出套利机会，但是没有执行功能，怎么执行？

我们需要添加一个功能让Peregrine执行套利，而不是打印套利

```python
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford

loop = asyncio.get_event_loop()
graph = loop.run_until_complete(load_exchange_graph('hitbtc'))
paths = bellman_ford(graph, 'BTC', unique_paths=True)
for path in paths:
    print_profit_opportunity_for_path(graph, path)
```

把最后那段代码改一下就能执行套利了，怎么改？

multiple\_coins\_one\_exchange.py（[peregrine](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74)/[examples](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74/examples)/[**multiple\_coins\_one\_exchange.py**](https://github.com/wardbradt/peregrine/blob/58f160a15bea2b26a4c9353d9a10fdc878f00f74/examples/multiple_coins_one_exchange.py) ）

* asyncio
* load\_exchange\_graph（[peregrine](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74)/[peregrinearb](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb)/[utils](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb/utils)/[**single\_exchange.py**](https://github.com/wardbradt/peregrine/blob/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb/utils/single_exchange.py) ，**41行**）
* print\_profit\_opportunity\_for\_path（[peregrine](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74)/[peregrinearb](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb)/[utils](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb/utils)/[**general.py**](https://github.com/wardbradt/peregrine/blob/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb/utils/general.py)**，17行** ）
* bellman\_ford（from .bellmannx import bellman\_ford，[peregrine](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74)/[peregrinearb](https://github.com/wardbradt/peregrine/tree/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb)/[**bellmannx.py**](https://github.com/wardbradt/peregrine/blob/58f160a15bea2b26a4c9353d9a10fdc878f00f74/peregrinearb/bellmannx.py)**，48行** ）

{% embed url="https://www.programcreek.com/python/?code=wardbradt%2Fperegrine%2F" %}



