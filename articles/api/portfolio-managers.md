---
sidebar_current: "api-portfolio-managers"
---

# Portfolio Managers

Portfolio managers add a modular methods, and a separate of concerns, to think
about trading strategies. Using them, one can build algorithms focusing on
detecting signals while managers will take care of analyzing those
opportunities to compute assets allocation.

## API

This design allows implementations that are simpler, more readable and easier
to test. Keeping this in mind, the class logic is really close to algorithm one.

First, a new manager must inherit from `PortfolioFactory`. This class provide
most of the necessary setup, keep track of the situation state and, if asked,
apply some general portfolio constraints (like maximum investment per stock).

```python
from intuition.api.portfolio import PortfolioFactory

class MyManager(PortfolioFactory):
    '''
    Manages portfolio during simulation, and stays aware of the situation
    through the update() method.

    User strategies call it with a dictionnnary of detected opportunities (i.e.
    buy or sell signals). Then the optimize() function computes assets
    allocation, returning a dictionnary of symbols with their weigths or amount
    to reallocate.

    {'buy': zipline.Positions}    __________________________      _____________
    signals                   --> |                         | -> |            |
                                  | trade_signals_handler() |    | optimize() |
    orders  {'google': 34}    <-- |_________________________| <- |____________|

    This is an abstract class. Inheretid class will eventually overwrite
    optmize() to expose their own assets allocation strategy.
    '''
```

The same way we wrote an algorithm, an `initialize` method is provided to setup
everything using the manager configuration.

```python
    def initialize(properties):
        '''
        This method runs only once and before any trade. Use it to initialize
        your strategy.
        '''
        self.constant_amount = properties.get('constant_amount', 100)
```

With everything prepared, we can implement how signals will be processed to
provide a smart asset allocation.

```python
    def optimize(self, to_buy={}, to_sell={}):
        '''
        Specifies the portfolio's allocation strategy. Attributes available:
        self.portfolio    : zipline portfolio object
        self.perfs        : zipline perf_tracker object
        self.date         : current event datetime

        to_buy and to_sell are dictionaries keyed by sids, with values set by
        the algorithm.

        It must return the following informations:
            allocations: dict
                Symbols with their :
                    -> weigths -> for buy: according the whole portfolio value (must be floats)
                               -> for sell: according total symbol position in portfolio
                    -> amount: number of stocks to process (must be ints)
            e_ret: float
                Expected return
            e_risk: float
                Expected risk
        '''
        allocations = {}
        for sid in to_buy:
            allocations[sid] = self.constant_amount
        for sid in to_sell:
            allocations[sid] = -self.constant_amount

        return allocations, 0, 1
```

The results returned will be handled by the algorithm `process_orders()`
method.

## Usage

Here is a minimal implementation: It buys a constant amount of every
buy signals, and ignores sell ones.

```python
from intuition.api.portfolio import PortfolioFactory

class TestPortfolio(PortfolioFactory):
    ''' Returns minimal portfolio for demonstration '''

    def initialize(self, properties):
          self.constant = properties.get('constant', 100)

    def optimize(self, date, to_buy, to_sell, parameters):
        allocation = {sid: self.constant for sid in to_buy}
        return allocation, 0, 1
```

And a minimal usage, ignoring sophisticated portfolio or positions tracking
(covered soon).

```python
manager = TestPortfolio(properties={'constant': 25})
manager.log.debug('Constant amount: ' + manager.constant)
manager.advise(constant=54, hello='world')
manager.log.debug('Manager configuration: ' + manager.properties)

manager.trade_signals_handler({'buy': ['goog', 'aapl']})
# Out[4]: {'aapl': 54, 'goog': 54}
```


## Portfolio state tracking

... Available soon ...

---
##### last modified on: March 19, 2014
---
