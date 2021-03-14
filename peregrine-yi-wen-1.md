# Peregrine译文1

## Peregrine

一个Python库，它提供多种算法来检测48个国家120多个加密货币交易所的38000多个交易对的套利机会

#### 安装·使用·即将发生的变化

### Install <a id="kkwbbab8eh"></a>

1. 确保已安装pip。
2. 在命令行中运行以下命令：`pip install git+https://github.com/wardbradt/peregrine`

### Usage <a id="xvns591thd"></a>

本节简要介绍Peregrine的功能。peregrine/Examples中提供了演示更多功能的示例。

#### Multiples Exchange/ One Currency

```text
from peregrinearb import get_opportunity_for_market
import asyncio
collections_dir = '/Users/wardbradt/cs/peregrine/'
opportunity = asyncio.get_event_loop().run_until_complete(get_opportunity_for_market("BTC/USD", collections_dir))
print(opportunity)
```

在撰写本文时，这将在不到一秒钟内打印以下内容。

```text
{'highest_bid': {'exchange': <ccxt.async.lakebtc.lakebtc object at 0x10ea50518>, 'price': 11750.59},
'lowest_ask': {'exchange': <ccxt.async.gdax.gdax object at 0x10ea50400>, 'price': 8450.01}}

```

如果要指定要在哪些交易所查找机会：

```text
from peregrinearb import get_opportunity_for_market
import asyncio

collections_dir = '/Users/wardbradt/cs/peregrine/'
opportunity = asyncio.get_event_loop().run_until_complete(get_opportunity_for_market("BTC/USD", collections_dir, exchanges=["anxpro", "bitbay", "coinfloor", "gemini", "livecoin"]))
print(opportunity)
```

如果你想在某个国家的交易所里找到机会，你可以这样做：

```text
from peregrinearb import build_specific_collections, get_opportunity_for_market

us_eth_btc_exchanges = build_specific_collections(countries=['US'])
collections_dir = '/Users/wardbradt/cs/peregrine/'
opportunity = get_opportunity_for_market("ETH/BTC", collections_dir, us_eth_btc_exchanges["ETH/BTC"])
print(opportunity)
```

1本例中代替“US”的可接受参数为“PA”、“AU”、“CA”、“JP”、“SG”、“HK”、“NZ”、“IE”、“CN”、“KR”、“IL”、“MT”、“EU”、“VG”、“GB”、“RU”、“PL”、“SC”、“MX”、“NL”、“BR”、“PH”、“UA”、“TR”、“IS”、“TH”、“DE”、“CY”、“CL”、“TW”、“ID”、“UK”、“in”、“VN”、“BG”、“CZ”、“ES”、“SE”、“VC”、“ZA”、“CH”、“TZ”、“FR”、“AR”、“VE”、“PK”和“AT”。

#### One Exchange/ Multiple Currencies

```text
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford
graph = asyncio.get_event_loop().run_until_complete(load_exchange_graph('hitbtc'))

paths = bellman_ford(graph)
for path in paths:
    print_profit_opportunity_for_path(graph, path)
```

这将打印给定交易所（在本例中为HitBTC）上的所有套利机会。在撰写本文时，打印出来的第一个机会是：

```text
Starting with 100 in BTC
BTC to USDT at 7955.100000 = 795510.000000
USDT to NEO at 0.016173 = 12866.084425
NEO to ETH at 0.110995 = 1428.071041
ETH to XLM at 2709.292875 = 3869062.695088
XLM to BTC at 0.000026 = 100.208724

```

如果您想计算交易费用，请在调用`load_exchange_graph`时设置`fees=True`。

```text
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford

graph = asyncio.get_event_loop().run_until_complete(load_exchange_graph('gdax', fees=True))

paths = bellman_ford(graph)
for path in paths:
    print_profit_opportunity_for_path(graph, path)
```

要找到可用于执行opportunity的最大容量，请在调用`bellman_ford`时设置`depth=True`。据我所知，唯一能够同时获取所有市场最高价格水平的交易量的交易所是Binance。

```text
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford

graph = asyncio.get_event_loop().run_until_complete(load_exchange_graph('binance'))

paths = bellman_ford(graph, depth=True)
for path, starting_amount in paths:
    # Note that depth=True and starting_amount are set in this example
    print_profit_opportunity_for_path(graph, path, depth=True, starting_amount=starting_amount)
```

这将产生：

```text
Starting with 0.25 in BTC
BTC to USDT at 7955.100000 = 1988.775
USDT to NEO at 0.016173 = 32.1652110625
NEO to ETH at 0.110995 = 3.5701776025
ETH to XLM at 2709.292875 = 9,672.65673772
XLM to BTC at 0.000026 = 0.25052181

```

#### Multiple Exchanges/ Multiple Currencies

```text
from peregrinearb import create_weighted_multi_exchange_digraph, bellman_ford_multi, print_profit_opportunity_for_path_multi


graph = create_weighted_multi_exchange_digraph(['kraken', 'bittrex', 'gemini'], log=True)
graph, paths = bellman_ford_multi(graph, 'ETH')
for path in paths:
    print_profit_opportunity_for_path_multi(graph, path)
```

这将打印给定交易所的所有套利机会。在撰写本文时，打印出来的第一个机会是：

```text
Starting with 100 in ETH
ETH to ANT at 204.26088199848851 = 20426.08819984885 on bittrex for ANT/ETH
ANT to BTC at 0.00034417000000000003 = 7.03004677574198 on bittrex for ANT/BTC
BTC to MLN at 136.57526594618665 = 960.1305080110928 on bittrex for MLN/BTC
MLN to BTC at 0.0073799999999999985 = 7.085763149121863 on kraken for MLN/BTC
BTC to GNO at 98.03921568627446 = 694.6826616786137 on bittrex for GNO/BTC
GNO to BTC at 0.010300000000000002 = 7.155231415289722 on kraken for GNO/BTC
BTC to GNO at 98.03921568627446 = 701.493276008796 on bittrex for GNO/BTC
GNO to BTC at 0.010300000000000002 = 7.2253807428906 on kraken for GNO/BTC
BTC to MLN at 136.57526594618665 = 986.8082965227394 on bittrex for MLN/BTC
MLN to BTC at 0.0073799999999999985 = 7.282645228337815 on kraken for MLN/BTC
BTC to USD at 7964.809999999999 = 58004.8855411173 on gemini for BTC/USD
USD to ETH at 0.0017965900720432618 = 104.21100149317708 on kraken for ETH/USD

```

你想把交易费用算进去吗。在上面的例子中，只需在调用`create_weighted_multi_exchange_digraph`时将`fees`设置为`True`。例如，下面的代码打印出给定交易所中找到的所有机会，同时计算费用：

```text
from peregrinearb import create_weighted_multi_exchange_digraph, bellman_ford_multi, print_profit_opportunity_for_path_multi


graph = create_weighted_multi_exchange_digraph(['exmo', 'binance', 'bitmex', 'bittrex', 'gemini', 'kraken'], log=True)


graph, paths = bellman_ford_multi(graph, 'ETH', unique_paths=True)
for path in paths:
    print_profit_opportunity_for_path_multi(graph, path)
```

这两种印刷品中最赚钱的是：

```text
Starting with 100 in ETH
ETH to LTC at 3.2955444239388347 = 329.55444239388345 on binance for LTC/ETH
LTC to USD at 173.00829999999996 = 57015.65383601369 on exmo for LTC/USD
USD to XRP at 1.4110342881332016 = 80451.04252294863 on kraken for XRP/USD
XRP to USD at 0.739201 = 59469.49108400615 on exmo for XRP/USD
USD to BTC at 0.00011205737337516807 = 6.663994966831705 on bitmex for BTC/USD
BTC to XRP at 12599.218848431392 = 83961.13099195795 on bittrex for XRP/BTC
XRP to USD at 0.739201 = 62064.15199038631 on exmo for XRP/USD
USD to BTC at 0.00011205737337516807 = 6.954745852799899 on bitmex for BTC/USD
BTC to XRP at 12599.218848431392 = 87624.36503464654 on bittrex for XRP/BTC
XRP to RUB at 39.120000000000005 = 3427865.160155373 on exmo for XRP/RUB
RUB to USD at 0.018667164457718873 = 63988.522683505194 on exmo for USD/RUB
USD to XRP at 1.4110342881332016 = 90289.99955341498 on kraken for XRP/USD
XRP to RUB at 39.120000000000005 = 3532144.7825295944 on exmo for XRP/RUB
RUB to USD at 0.018667164457718873 = 65935.1275439536 on exmo for USD/RUB
USD to BCH at 0.000949667616334283 = 62.61645540736334 on kraken for BCH/USD
BCH to ETH at 1.8874401 = 118.18480885571941 on bittrex for BCH/ETH

```

### Upcoming Changes <a id="x8ss3v4l2z"></a>

#### In Development

在`dev`分支上正在进行以下更改，或很快将进行更改。

**General**

* Adding logging
* 从python3.6-&gt;python3.7升级以启用与新ccxt版本的兼容性。多恩
* 改进模块化，使库能够用于user-provided价格数据，例如non-integrated交换的数据或通过WebSocket接收的数据。
* 更改模块结构以分离特性和依赖项
* 贬低某些功能
* 总体结构调整和更改以提高可用性和性能

**Bellman Ford**

* 添加平均价格（`best_bid`+`best_ask`/2）的功能，而不是三角套利的最佳价格（这里要求）
* 确保所有图的"k-core“为2以提高性能
* 添加排除或独占某些货币的功能（此处要求）
* 固定深度功能

#### Planned

这些可能会有变化。我目前打算在完成上面列出的那些之后实施它们。

**Bellman Ford**

* 使用`scipy`稀疏矩阵代替`networkx`图来提高Bellman Ford的性能，这比这个项目更像是对scipy的升级。这是因为scipy的Bellman-Ford实现目前会在负循环是found--there时引发一个错误，而不是追溯负循环的机制。
* 实施日元对贝尔曼福特的改进可能只会在升级到scipy之后/同时进行，这样就不会再做两次了。

