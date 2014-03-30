---
sidebar_current: "api"
---

API
===

Here are some examples to get you started. To make your work available, just
feed the [Intuition][3] configuration (like above) a path it can find in the
environment variable `$PYTHONPATH`.

Everything is done to make writing modules easy, fun and efficient. So your
feedback, advices and contributions are happily welcome

* First, a classic buy and hold strategy, with a plugin which stores metrics in
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
            self.use(database.RethinkdbBackend(self.identity, reset=True)
                     .save_portfolio)

    def event(self, data):
        signals = {}

        if self.day == 2:
            for ticker in data:
                signals[ticker] = data[ticker].price

        return signals
```

* Here is the Fair manager example, which allocates the same weight in the
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

* Now a source for backtests taking advantage of the awesome [Quandl][2] project.

```python
from intuition.zipline.data_source import DataFactory
from intuition.data.quandl import DataQuandl

class QuandlSource(DataFactory):
    '''
    Fetchs data from quandl.com
    '''
    def get_data(self):
        return DataQuandl().fetch(self.sids,
                          start_date=self.start,
                          end_date=self.end,
                          returns='pandas')

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



---
##### last modified on: March 19, 2014
---
