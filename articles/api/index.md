---
sidebar_current: "api"
---

API
===

This sections describes the tools available to help you build modules and
extensions for `intuition`. Everything is done to make it easy, fun and
efficient. So your feedback, advices and contributions are happily welcome.

A compilation of official modules are hosted [on github under the name of the
Insights project](https://github.com/intuition-io/insights). You can also learn more about them
[here in the Insights section](/articles/insights).

They are good examples to get some inspiration. When you will be a
trading/coding ninja please considere to submit pull requests !

You will also find how to use individually the sub-parts of the project, giving
you the ability to craft whole new financial (or not) applications.


Modules Usage
-------------

There are 4 module types that can be combined as you wish :

* Data sources : As the name suggests it, their role is to gather the data to
feed your strategy, in backtest and live mode.

* Algorithms : The central point of your strategy. They receive the data as
events and let you do pretty much whatever you want with it : compute
indicators, detect opportunities, process orders, store informations, ...

* Portfolio managers : They allow you to compute sophisticated (or not) assets
allocation from signals triggered by the algorithm.

* Contexts : Everything can be customized in `intuition`, which can lead to
complex configurations. `Contexts loaders` take care of loading and
normalizing from your favorite location those carefully drafted compilation
of parameters.

Once provided to the configuration, those modules are dynamically loaded.
Just drop something like below:

```yaml
modules:
  manager: insights.managers.optimalfrontier.OptimalFrontier
  algorithm: insights.algorithms.dummy.BuyAndHold
  backtest: insights.sources.backtest.yahoo.YahooOHLC
```

Make sure the given path is registered in the `PYTHONPATH` environment
variable.

Examples
--------

First, a classic buy and hold strategy, with a plugin which stores metrics in
[rethinkdb](www.rethinkdb.com):

```python
from intuition.zipline.algorithm import TradingFactory
import insights.plugins.database as database

class BuyAndHold(TradingFactory):
    '''
    Simpliest algorithm ever, just buy every stocks at the first frame
    '''
    def initialize(self, properties):

        if properties.get('save'):
            self.use(database.RethinkdbFinance(self.identity, reset=True)
                     .save_portfolio)

    def event(self, data):
        signals = {}

        if self.days == 2:
            for ticker in data:
                signals[ticker] = data[ticker].price

        return signals
```

---

Here is the Fair manager example, which allocates the same weight in the
portfolio to all of your assets:

```python
from intuition.zipline.portfolio import PortfolioFactory

class Fair(PortfolioFactory):
    '''
    Dispatch equals weigths for buy signals and give up everything on sell ones
    '''
    def optimize(self, date, to_buy, to_sell, parameters):
        # The algorithm fills 'to_buy' and 'to_sell' dicts by detecting
        # buy and sell signals
        # This dictionnary holds portfolio manager recommendations
        allocations = {}

        if to_buy:
            # Split the portfolio in equal parts for each stock to buy
            fraction = round(1.0 / float(len(to_buy)), 2)
            for s in to_buy:
                allocations[s] = fraction

        for s in to_sell:
            # Simply sells every stocks
            allocations[s] = - self.portfolio.positions[s].amount

        expected_return = 0
        expected_risk = 1
        return allocations, expected_return, expected_risk
```

---

Now a source for backtests taking advantage of the awesome
[Quandl](http://quandle.com) project.

```python
from intuition.zipline.data_source import DataFactory
from intuition.data.quandl import DataQuandl

class QuandlSource(object):
    '''
    Fetchs data from quandl.com
    '''
    def __init__(self, sids, properties):
        pass

    def get_data(self, sids, start, end):
        return DataQuandl().fetch(
            sids, start=start, end=end)

    @property
    def mapping(self):
        return {
            'dt': (lambda x: x, 'dt'),
            'sid': (lambda x: x, 'sid'),
            'price': (float, 'Close'),
            'volume': (int, 'Volume'),
            'open': (int, 'Open'),
            'low': (int, 'Low'),
            'high': (int, 'High'),
        }
```

Follow the links in the navbar to learn how you can use and extend pretty much
every submodules of the project.


---
##### last modified on: March 19, 2014
---
