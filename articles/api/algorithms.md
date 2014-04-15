---
sidebar_current: "api-algorithms"
---

# Algorithms

Algorithms are classes where you will process stock data to detect
opportunities and process orders. You have full control over their
configurations and a working implementation only needs 2 methods :

* `initialize(self, properties)` called once before anything else.
* `event(self, data)` called for each event with the current related data.

The algorithm doesn't care if you're in backtest or live mode, it works exactly
the same.

Note that `intuition` algorithms are fully compatible with ones from
[zipline](https://github.com/quantopian/zipline).

## API

Your implementation needs to inherit from the TradingFactory abstract class.
It does the hard work, like keeping track of your portfolio and its
performances, and lets you focus on the algo.

```python
from intuition.api.algorithm import TradingFactory

class MyAlgo(TradingFactory):
  '''
  This class detects "buy" and "sell" signals, and process transactions. You
  can also use a portfolio manager to compute sophisticaed (or not) stocks
  allocation.

  The parent class provides the following useful attributes :
    - manager - Manager object specified on command line
    - portfolio - Portfolio informations
    - perf_tracker - Trading metrics
    - datetime - Current datetime
    - days - Day counter
    - logger
  '''
```

You will probably want to initialize your strategy and that's the point of the
next method. This is also the place to register middlewares ([learn more
here](/articles/api/plugins.html)).

```python
  def initialize(self, properties):
    '''
    This function runs once, before any trading event.
    '''
    # `properties` is a dictionnary storing parameters you set outside.
    self.short_window = properties.get('short_window', 15)
    self.long_window = properties.get('long_window', 30)

    # Intuition comes with battery included
    # Middlewares are trading tools. self.use() is sugar syntax that tells the
    # algorithm to call the provided function after each event
    if properties.get('save'):
        self.use(database.RethinkdbFinance(self.identity, True)
                 .save_portfolio)
```

Sometimes you may want to run some code before the first trading event, but
after everything has been prepared and with the knowledge of the available
data.

```python
  def warm(self, data):
    '''
    An other function ran once, after everything has been initialized, the
    very first time of trading
    '''
    # Fit a model before backtesting for example
    self.training_model = train_on_past_data(data.keys())
```

Ok we're ready to trade ! The `event` function is called with new data at the
frequency you decided.

```python
  def event(self, data):
    '''
    If you can detect trading opportunities, this is where you place orders or
    fill the signals dictionnary like so {'buy': {sid: data[sid]}, 'sell': {}}.
    The portfolio manager will use it to compute the allocation.
    '''
    signals = {'buy': {}, 'sell': {}}

    for ticker in data:

        if great_prospect():
            # Currently, if you want to use weight-based strategy allocation,
            # you must provide the current sid price
            signals['buy'][ticker] = data[ticker]

        if take_profit():
            signals['sell'][ticker] = data[ticker]

        if i_dont_care_about_the_portfolio_manager():
            # Buy 120 stocks of the sid
            self.order(sid, 120)
            # A negative amount would sell this sid

    return signals
```

By default, the algorithm will simply execute whatever the portfolio manager
returned. If you want to overload this behavior, add the following function.

```python
def process_orders(self, allocation):
    '''
    Called eveytime you triggered signals if your algorithm uses a
    portfolio manager
    <allocation> is a dictionnary storing what the manager decided.
    '''
    for sid, amount in orderbook.iteritems():
        if the_universe_agree():
            self.order(sid, amount)
```

## Usage

We're going to see how we can use a standalone fake algorithm, outside
`intuition`.

First we need a dummy implementation :

```python
from intuition.api.algorithm import TradingFactory

class FakeAlgorithm(TradingFactory):

    def initialize(self, properties):
        self.custom_amount = properties.get('custom_amount', 100)

    def warm(self, data):
        print 'Nothing to do here'

    def event(self, data):
        for sid in data:
            # This crazy algo always buys
            self.order(sid, self.custom_amount)
```

Now let's setup some data to feed to the algorithm.

```python
import pandas as pd
from intuition.api.datafeed import HybridDataFactory
from intuition.data.universe import Market
from insights.sources.backtest.yahoo import YahooPrices

# The trading timeline
dates = pd.date_range('2012/01/01', '2012/06/01', tz=pytz.utc)
# Market informations. Here we trade 5 stocks from the cac40
market = Market()
market.parse_universe_description('stocks:paris:cac40,5')
data_source = HybridDataFactory(
    universe=market,
    index=,
    backtest=YahooPrices
)
```

And go !

```python
algo = FakeAlgorithm(properties={'custom_amount': 203})
results = algo.run(data_source)
```

Learn more about the `results` returned and how you can manipulate it using
`analyzers` in [this section](/articles/api/analyzers).


## Simulation Environment

... Available soon ...


---
##### last modified on: March 19, 2014
---
