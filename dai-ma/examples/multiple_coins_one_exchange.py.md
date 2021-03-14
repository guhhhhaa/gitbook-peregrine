# multiple\_coins\_one\_exchange.py🚩

```python
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford

loop = asyncio.get_event_loop()
graph = loop.run_until_complete(load_exchange_graph('hitbtc'))
paths = bellman_ford(graph, 'BTC', unique_paths=True)
for path in paths:
    print_profit_opportunity_for_path(graph, path)
```

multiple\_coins\_one\_exchange.py

* asyncio
* load\_exchange\_graph
* print\_profit\_opportunity\_for\_path
* bellman\_ford（from .bellmannx import bellman\_ford）

