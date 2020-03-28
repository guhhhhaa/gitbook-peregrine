# 游隼

## 游隼

一个Python库，提供了多种算法来检测48个国家/地区超过38,000个交易对的120多种加密货币交易所的套利机会

## 游隼 <a id="peregrine"></a>

一个Python库，提供了多种算法来检测48个国家/地区超过38,000个交易对的120多种加密货币交易所的套利机会

#### [安装](https://github.com/wardbradt/peregrine#install) · [用法](https://github.com/wardbradt/peregrine#usage) · [即将改变](https://github.com/wardbradt/peregrine#upcoming-changes) <a id="install-usage-upcoming-changes"></a>

### 安装 <a id="install"></a>

1. 确保已[安装pip](https://pip.pypa.io/en/stable/installing/)。
2. 在命令行中运行以下命令：

   ```text
   pip install git+https://github.com/wardbradt/peregrine
   ```

### 用法 <a id="usage"></a>

本节简要介绍了Peregrine的功能。展示更多功能的[示例](https://github.com/wardbradt/peregrine/tree/master/examples)在[peregrine / examples](https://github.com/wardbradt/peregrine/tree/master/examples)中可用。

#### 倍数兑换/一种货币 <a id="multiples-exchange-one-currency"></a>

```text
from peregrinearb import get_opportunity_for_market
import asyncio
collections_dir = '/Users/wardbradt/cs/peregrine/'
opportunity = asyncio.get_event_loop().run_until_complete(get_opportunity_for_market("BTC/USD", collections_dir))
print(opportunity)
```

在撰写本文时，这将在不到一秒钟的时间内打印出以下内容。

```text
{'highest_bid': {'exchange': <ccxt.async.lakebtc.lakebtc object at 0x10ea50518>, 'price': 11750.59},
'lowest_ask': {'exchange': <ccxt.async.gdax.gdax object at 0x10ea50400>, 'price': 8450.01}}
```

如果您想指定在哪些交易所寻找机会：

```text
from peregrinearb import get_opportunity_for_market
import asyncio

collections_dir = '/Users/wardbradt/cs/peregrine/'
opportunity = asyncio.get_event_loop().run_until_complete(get_opportunity_for_market("BTC/USD", collections_dir, exchanges=["anxpro", "bitbay", "coinfloor", "gemini", "livecoin"]))
print(opportunity)
```

如果您只想在某个国家/地区的交易所中寻找机会，可以这样做：

```text
from peregrinearb import build_specific_collections, get_opportunity_for_market

us_eth_btc_exchanges = build_specific_collections(countries=['US'])
collections_dir = '/Users/wardbradt/cs/peregrine/'
opportunity = get_opportunity_for_market("ETH/BTC", collections_dir, us_eth_btc_exchanges["ETH/BTC"])
print(opportunity)
```

在此示例中，代替“ US”接受的参数是“ PA”，“ AU”，“ CA”，“ JP”，“ SG”，“ HK”，“ NZ”，“ IE”，“ CN”，“ KR” '，'IL'，'MT'，'EU'，'VG'，'GB'，'RU'，'PL'，'SC'，'MX'，'NL'，'BR'，'PH'， 'UA'，'TR'，'IS'，'TH'，'DE'，'CY'，'CL'，'TW'，'ID'，'UK'，'IN'，'VN'，'BG '，'CZ'，'ES'，'SE'，'VC'，'ZA'，'CH'，'TZ'，'FR'，'AR'，'VE'，'PK'和'AT' 。

#### 一兑换/多种货币 <a id="one-exchange-multiple-currencies"></a>

```text
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford
graph = asyncio.get_event_loop().run_until_complete(load_exchange_graph('hitbtc'))

paths = bellman_ford(graph)
for path in paths:
    print_profit_opportunity_for_path(graph, path)
```

这将打印给定交易所（在本例中为HitBTC）上的所有套利机会。在撰写本文时，第一个打印出来的机会是：

```text
Starting with 100 in BTC
BTC to USDT at 7955.100000 = 795510.000000
USDT to NEO at 0.016173 = 12866.084425
NEO to ETH at 0.110995 = 1428.071041
ETH to XLM at 2709.292875 = 3869062.695088
XLM to BTC at 0.000026 = 100.208724
```

如果您想计入交易费用，请在致电时进行设置。`fees=Trueload_exchange_graph`

```text
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford

graph = asyncio.get_event_loop().run_until_complete(load_exchange_graph('gdax', fees=True))

paths = bellman_ford(graph)
for path in paths:
    print_profit_opportunity_for_path(graph, path)
```

要查找可用于执行机会的最大交易量，请在致电时进行设置。据我所知，唯一可以提供同时为所有市场获取最高价格数量的功能的交易所是Binance。`depth=Truebellman_ford`

```text
import asyncio
from peregrinearb import load_exchange_graph, print_profit_opportunity_for_path, bellman_ford

graph = asyncio.get_event_loop().run_until_complete(load_exchange_graph('binance'))

paths = bellman_ford(graph, depth=True)
for path, starting_amount in paths:
    # Note that depth=True and starting_amount are set in this example
    print_profit_opportunity_for_path(graph, path, depth=True, starting_amount=starting_amount)
```

这将输出：

```text
Starting with 0.25 in BTC
BTC to USDT at 7955.100000 = 1988.775
USDT to NEO at 0.016173 = 32.1652110625
NEO to ETH at 0.110995 = 3.5701776025
ETH to XLM at 2709.292875 = 9,672.65673772
XLM to BTC at 0.000026 = 0.25052181
```

#### 多次交换/多种货币 <a id="multiple-exchanges-multiple-currencies"></a>

```text
from peregrinearb import create_weighted_multi_exchange_digraph, bellman_ford_multi, print_profit_opportunity_for_path_multi


graph = create_weighted_multi_exchange_digraph(['kraken', 'bittrex', 'gemini'], log=True)
graph, paths = bellman_ford_multi(graph, 'ETH')
for path in paths:
    print_profit_opportunity_for_path_multi(graph, path)
```

这将打印给定交易所上的所有套利机会。在撰写本文时，第一个打印出来的机会是：

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

您是否要考虑交易费用。在上面的示例中，只需在调用`free`时设置为`True`即可。例如，以下代码在考虑费用的情况下打印出在给定交易所中找到的所有机会：`feesTruecreate_weighted_multi_exchange_digraph`

```text
from peregrinearb import create_weighted_multi_exchange_digraph, bellman_ford_multi, print_profit_opportunity_for_path_multi


graph = create_weighted_multi_exchange_digraph(['exmo', 'binance', 'bitmex', 'bittrex', 'gemini', 'kraken'], log=True)


graph, paths = bellman_ford_multi(graph, 'ETH', unique_paths=True)
for path in paths:
    print_profit_opportunity_for_path_multi(graph, path)
```

印出的两个中最赚钱的是：

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

### 即将发生的变化 <a id="upcoming-changes"></a>

#### 开发中 <a id="in-development"></a>

在`dev`分支上，正在进行或即将进行以下更改：

**一般**

* 添加日志
* 从 Python 3.6-&gt; Python 3.7升级以实现与新ccxt版本的兼容性。**完成**
* 改进模块性以使该库可用于用户提供的价格数据，例如来自非集成交易所的数据或通过WebSocket接收的数据。
* 更改模块结构以分离功能和依赖项
* 弃用某些功能
* 总体重组和变更，以提高可用性和性能

**贝尔曼·福特**

* 添加平均价格（`best_bid`+`best_ask` / 2）而不是三角套利的最佳价格的功能（[在此处请求](https://github.com/wardbradt/peregrine/issues/39)）
* 确保所有图的“ k-core”为2以改善性能
* 添加功能以排除或仅包含某些货币（[在此处请求](https://github.com/wardbradt/peregrine/issues/43)）
* 修复了交易深度功能

#### 计划 <a id="planned"></a>

这些内容可能会发生变化。我目前打算在上述列出的内容完成之后实施它们。

**贝尔曼·福特**

* 使用`scipy·稀疏矩阵代替`networkx\`图形以提高Bellman Ford性能

  比这个项目更像是对 scipy 的升级。这是因为 scipy 的 Bellman Ford 实现当前会在发现负环时引发`error`-没有任何机制可以追溯负环。

* 实施[Yen](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm#Improvements)对Bellman Ford [的改进](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm#Improvements)

  这可能只会在升级到scipy之后/同时进行，这样就不会再进行两次了。
