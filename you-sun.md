# 游隼



## 百富勤

一个Python库，提供了多种算法来检测48个国家/地区超过38,000个交易对的120多种加密货币交易所的套利机会

## 百富勤 <a id="peregrine"></a>

一个Python库，提供了多种算法来检测48个国家/地区超过38,000个交易对的120多种加密货币交易所的套利机会

#### [安装](https://github.com/wardbradt/peregrine#install) · [用法](https://github.com/wardbradt/peregrine#usage) · [即将改变](https://github.com/wardbradt/peregrine#upcoming-changes) <a id="install-usage-upcoming-changes"></a>

### 安装 <a id="install"></a>

1. 确保已[安装pip](https://pip.pypa.io/en/stable/installing/)。
2. 在命令行中运行以下命令：

   ```text
   点安装git + https：//github.com/wardbradt/peregrine
   ```

### 用法 <a id="usage"></a>

本节简要介绍了Peregrine的功能。展示更多功能的[示例](https://github.com/wardbradt/peregrine/tree/master/examples)在[peregrine / examples](https://github.com/wardbradt/peregrine/tree/master/examples)中可用。

#### 倍数兑换/一种货币 <a id="multiples-exchange-one-currency"></a>

```text
从peregrinearb import get_opportunity_for_market导入异步collections_dir ='/ Users / wardbradt / cs / peregrine /'机会= asyncio.get_event_loop（）。run_until_complete（get_opportunity_for_market（“ BTC / USD”，collections_dir））打印（机会）
```

在撰写本文时，这将在不到一秒钟的时间内打印出以下内容。

```text
{'highest_bid'：{'exchange'：<ccxt.async.lakebtc.lakebtc object at 0x10ea50518>，'price'：11750.59}，'lowest_ask'：{'exchange'：<ccxt.async.gdax.gdax对象，位于0x10ea50400>，'price'：8450.01}}
```

如果您想指定在哪些交易所寻找机会：

```text
从peregrinearb import get_opportunity_for_market导入异步​collections_dir ='/ Users / wardbradt / cs / peregrine /'机会= asyncio.get_event_loop（）。run_until_complete（get_opportunity_for_market（“ BTC / USD”，collections_dir，exchanges = [“ anxpro”，“ bitbay”，“ coinfloor”，“ gemini”，“ livecoin”]））打印（机会）
```

如果您只想在某个国家/地区的交易所中寻找机会，可以这样做：

```text
从peregrinearb导入build_specific_collections，get_opportunity_for_market​us_eth_btc_exchanges = build_specific_collections（countries = ['US']）collections_dir ='/ Users / wardbradt / cs / peregrine /'机会= get_opportunity_for_market（“ ETH / BTC”，collections_dir，us_eth_btc_exchanges [“ ETH / BTC”]）打印（机会）
```

1在此示例中，代替“ US”接受的参数是“ PA”，“ AU”，“ CA”，“ JP”，“ SG”，“ HK”，“ NZ”，“ IE”，“ CN”，“ KR” '，'IL'，'MT'，'EU'，'VG'，'GB'，'RU'，'PL'，'SC'，'MX'，'NL'，'BR'，'PH'， 'UA'，'TR'，'IS'，'TH'，'DE'，'CY'，'CL'，'TW'，'ID'，'UK'，'IN'，'VN'，'BG '，'CZ'，'ES'，'SE'，'VC'，'ZA'，'CH'，'TZ'，'FR'，'AR'，'VE'，'PK'和'AT' 。

#### 一兑换/多种货币 <a id="one-exchange-multiple-currencies"></a>

```text
导入异步从peregrinearb导入load_exchange_graph，print_profit_opportunity_for_path，bellman_ford图= asyncio.get_event_loop（）。run_until_complete（load_exchange_graph（'hitbtc'））​路径= bellman_ford（图）对于路径中的路径：    print_profit_opportunity_for_path（图形，路径）
```

这将打印给定交易所（在本例中为HitBTC）上的所有套利机会。在撰写本文时，第一个打印出来的机会是：

```text
从100开始BTC兑换USDT为7955.100000 = 795510.000000USDT至NEO的0.016173 = 12866.084425NEO对ETH在0.110995 = 1428.071041ETH到XLM的2709.292875 = 3869062.695088XLM到BTC的0.000026 = 100.208724
```

如果您想计入交易费用，请在致电时进行设置。`fees=Trueload_exchange_graph`

```text
导入异步从peregrinearb导入load_exchange_graph，print_profit_opportunity_for_path，bellman_ford​图= asyncio.get_event_loop（）。run_until_complete（load_exchange_graph（'gdax'，费用= True））​路径= bellman_ford（图）对于路径中的路径：    print_profit_opportunity_for_path（图形，路径）
```

要查找可用于执行机会的最大交易量，请在致电时进行设置。据我所知，唯一可以提供同时为所有市场获取最高价格数量的功能的交易所是Binance。`depth=Truebellman_ford`

```text
导入异步从peregrinearb导入load_exchange_graph，print_profit_opportunity_for_path，bellman_ford​图= asyncio.get_event_loop（）。run_until_complete（load_exchange_graph（'binance'））​路径= bellman_ford（图，深度=真）对于路径，在路径中的starting_amount：    ＃注意在此示例中设置depth = True和starting_amount    print_profit_opportunity_for_path（图形，路径，深度=真，starting_amount = starting_amount）
```

这将输出：

```text
从BTC的0.25开始BTC兑换USDT为7955.100000 = 1988.775USDT至NEO的0.016173 = 32.1652110625NEO对ETH在0.110995 = 3.5701776025ETH到XLM的2709.292875 = 9,672.65673772XLM到BTC的0.000026 = 0.25052181
```

#### 多次交换/多种货币 <a id="multiple-exchanges-multiple-currencies"></a>

```text
从peregrinearb导入create_weighted_multi_exchange_digraph，bellman_ford_multi，print_profit_opportunity_for_path_multi​​图= create_weighted_multi_exchange_digraph（['kraken'，'bittrex'，'gemini']，log = True）图，路径= bellman_ford_multi（图，'ETH'）对于路径中的路径：    print_profit_opportunity_for_path_multi（图形，路径）
```

这将打印给定交易所上的所有套利机会。在撰写本文时，第一个打印出来的机会是：

```text
以100开始在ANT / ETH的bittrex上，ETH到ANT的ETH为204.26088199848851 = 20426.08819984885ANT / BTC的Bittrex上的BTC的ANT为0.00034417000000000003 = 7.03004677574198在MLN / BTC的bittrex上，BTC到MLN的价格为136.57526594618665 = 960.1305080110928MLN / BTC上的kraken上的MLN为BTC为0.0073799999999999985 = 7.085763149121863针对GNO / BTC在bittrex上以98.03921568627446 = 694.6826616786137从BTC到GNOGNO / BTC的海妖状态下GNO以BTC为0.010300000000000002 = 7.155231415289722BTC到GNO的GNO在98.03921568627446 = 701.493276008796GNO / BTC在kraken上的GNO以BTC为0.010300000000000002 = 7.2253807428906BTC到MLN在MLN / BTC的bittrex上为136.57526594618665 = 986.8082965227394MLN / BTC上的ken上的MLN为BTC为0.0073799999999999985 = 7.282645228337815BTC / USD在gemini上的USD 7964.809999999999 = 58004.8855411173ETH / USD的海妖中，美元对ETH在0.0017965900720432618 = 104.21100149317708
```

您是否要考虑交易费用。在上面的示例中，只需在调用时设置为即可。例如，以下代码在考虑费用的情况下打印出在给定交易所中找到的所有机会：`feesTruecreate_weighted_multi_exchange_digraph`

```text
从peregrinearb导入create_weighted_multi_exchange_digraph，bellman_ford_multi，print_profit_opportunity_for_path_multi​​图= create_weighted_multi_exchange_digraph（[['exmo'，'binance'，'bitmex'，'bittrex'，'gemini'，'kraken']，log = True）​​图，路径= bellman_ford_multi（图，'ETH'，unique_paths = True）对于路径中的路径：    print_profit_opportunity_for_path_multi（图形，路径）
```

印出的两个中最赚钱的是：

```text
以100开始以LTC / ETH的余额为准的ETH到LTC的价格为3.2955444239388347 = 329.55444239388345LTC / USD在exmo上的LTC兑美元汇率为173.00829999999996 = 57015.65383601369XRP / USD在kraken上的美元兑XRP在1.4110342881332016 = 80451.04252294863XRP / USD的交易价格XRP兑换为USD 0.739201 = 59469.49108400615BTC / USD在bitmex上的BTC USD为0.00011205737337516807 = 6.663994966831705BTC到XRP的价格为12599.218848431392 = 83961.13099195795，适用于XRP / BTC的bittrexXRP / USD的交易价格XRP为USD在0.739201 = 62064.15199038631BTC / USD在bitmex上的USD兑换为BTC在0.00011205737337516807 = 6.954745852799899BTC到XRP的价格为12599.218848431392 = 87624.36503464654，用于XRP / BTC的bittrexXRP / RUB上的XRP到RUB的39.120000000000005 = 3427865.160155373在USD / RUB上，exmo上的RUB为USD在0.018667164457718873 = 63988.522683505194XRP / USD在kraken上的美元对XRP为1.4110342881332016 = 90289.99955341498XRP / RUB上的XRP到RUB的39.120000000000005 = 3532144.7825295944在USD / RUB上，exmo上的RUB为USD在0.018667164457718873 = 65935.1275439536BCH / USD的海妖中，美元兑BCH的价格为0.000949667616334283 = 62.61645540736334BCH / ETH的bittrex上BCH到ETH的价格为1.8874401 = 118.18480885571941
```

### 即将发生的变化 <a id="upcoming-changes"></a>

#### 开发中 <a id="in-development"></a>

分支机构正在进行或即将进行以下更改。`dev`

**一般**

* 添加日志
* 从Python 3.6-&gt; Python 3.7升级以实现与新ccxt版本的兼容性。**完成**
* 改进模块性以使该库可用于用户提供的价格数据，例如来自非集成交易所的数据或通过WebSocket接收的数据。
* 更改模块结构以分离功能和依赖项
* 弃用某些功能
* 总体重组和变更，以提高可用性和性能

**贝尔曼·福特**

* 添加平均价格（+ / 2）而不是三角套利的最佳价格的功能（`best_bidbest_ask`[在此处请求](https://github.com/wardbradt/peregrine/issues/39)）
* 确保所有图的“ k核心”为2以改善性能
* 添加排除或排除某些货币的功能（[在此处请求](https://github.com/wardbradt/peregrine/issues/43)）
* 深度固定功能

#### 计划 <a id="planned"></a>

这些内容可能会发生变化。我目前打算在上述列出的内容完成之后实施它们。

**贝尔曼·福特**

* 使用稀疏矩阵代替图形以提高Bellman Ford性能`scipynetworkx`

  比这个项目更像是对scipy的升级。这是因为scipy的Bellman Ford实现当前会在发现负周期时引发错误-没有任何机制可以追溯负周期。

* 实施[Yen](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm#Improvements)对Bellman Ford [的改进](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm#Improvements)

  这可能只会在升级到scipy之后/同时进行，这样就不会再进行两次了。

