# CHANGELOG.md

## Changelog

**This document is no longer up-to-date.**

All notable changes to this project will be documented in this file. As this project was started for a class which requires I \(wardbradt\) record my daily changes, I will also daily record changes which may not be "notable."

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/).

### \[0.3.4\] - 2018-02-24

#### Added

* Some comments in bellman\_multi\_graph.py

### \[0.3.3\] - 2018-02-24

#### Added

* StackSet \(In utils/data\_structures.py\)
* get\_greatest\_edge\_in\_bunch, get\_least\_edge\_in\_bunch \(In utils/graph\_utils.py\)
* print\_profit\_opportunity\_for\_path\_multi \(In utils/general.py\)
* next\_to\_each\_other and last\_index\_in\_list \(In utils/misc.py\)
* tests/test\_misc.py
* tests/bellman\_multi\_graph\_test.py
* statement in print\_profit\_opportunity\_for\_path to account for empty paths

#### Changed

* bellmannx.py with comments and cleaner code, also made it so `bellman_ford` only returns the arbitrage loop \(it cuts off extra trades\)
* bellman\_multi\_graph.py majorly. Look at comments to see how it works.
* Refactored utils and imports
* draw\_graph\_to\_file\(graph, dot\_name: str, to\_file: str\) to draw\_graph\_to\_png\(graph, to\_file: str\)
* README.md to account for function name changes

### \[0.3.2b\] - 2018-02-20

#### Added

* Explicit python decorator to README pre-formatted blocks
* Edge bunch definition to glossary.md
* examples/find\_opportunity\_for\_one\_coin\_across\_exchanges.py
* Reformatted README examples
* Two to-dos in README

#### Changed

* Added some imports to **init**.py files
* Marked some functions in bellman\_multi\_graph.py as protected

### \[0.3.1b\] - 2018-02-20

#### Changed

* **init**.py files \[0.3.0b\] - 2018-02-20

#### Added

* Cross Exchange Bellman Ford \(beta\)

### \[0.2.6\] - 2018-02-17 to 2018-02-19

#### Added

* Bellman Ford algorithm for Networkx graphs
* Beta Visualization Website \(Thanks to @ksun0\)

#### Changed

* Refactored utils
* Added `edge_bunches` for Networkx \(Available at [wardbradt/networkx](https://github.com/wardbradt/networkx)\)
* Deprecated bellman.py
* ExchangeGraphBuilder to ExchangeMultiGraphBuilder

#### Removed

* SingularlyAvailableExchangeError

### \[0.2.5\] - 2018-02-11

#### Added

* Created ExchangeMultiGraphBuilder
* Researched Networkx
* Did some graph theory research \(mainly on k-core and multigraphs\)
* Tests

#### Changed

* Moved `_get_exchange` to utils
* test\_build\_collections.py to test\_build\_markets.py

### \[0.2.4\] - 2018-02-08

#### Changed

* Continued Bellman Ford development
* Made Bellman Ford asynchronous

### \[0.2.4\] - 2018-02-07

#### Added

* Began to implement Bellman Ford algorithm

#### Changed

* readme and glossary

### \[0.2.3\] - 2018-02-06

#### Added

* started js-dev branch
* examples/restrict\_collections\_with\_kwargs\_dict\_argument.py
* glossary.md
* build\_collections
* test\_build\_collections

#### Changed

* Made it so that values of any type can be entered into kwargs instead of just `str`, `list`, and `dict` objects. Values which are not `list` or `dict` objects are treated as `str` objects previously were.

### \[0.2.2\] - 2018-02-06

#### Added

* Empty `test_kwargs_with_dict_as_rule` method

#### Changed

* Rules in async\_build\_markets.py to \*\*kwargs
* Updated tests to reflect this

### \[0.2.1\] - 2018-02-06

#### Added

* outliers.py

#### Changed

* Added `ccxt_errors` to several methods in async\_build\_markets.py
* Moved classes `SingularlyAvailableExchangeError`, `SingularlyAvailableExchangeError`, and `get_exchanges_for_market` from async\_find\_opportunities.py to utils.py so that they can be accessed from all files
* Added \(but did not implement\) `from_json` argument to `get_exchanges_for_market`

### \[0.2.0\] - 2018-02-05

#### Added

* Cython version of code \(under the /cythonperegrine directory\)
* Examples directory \(/examples\)
* First speed comparison \(in /examples\)
* `calculate_time` in examples/calculate\_time.py, a utility for measuring the speed of functions
* Type aliases to some function declarations in peregrine/

#### Removed

* Unnecessary print statement at start of async\_find\_opportunities.py

#### Changed

* Cleaned up some doc strings
* test\_bid\_and\_ask to \_test\_bid\_and\_ask to denote that it should not be accessed by users

### \[0.1.1\] - 2018-02-01

#### Added

* Unit tests for async\_build\_markets.py in peregrine/tests/test\_build\_collections.py

#### Changed

* Moved src files to peregrine subdirectory.

### \[0.1.0\] - 2018-02-01

#### Added

* Initial Commit

