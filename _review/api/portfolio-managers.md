---
sidebar_current: "api-portfolio-managers"
---

Portfolio Managers
==================


API
---

```python
from portfolio import PortfolioFactory

class MyManager(PortfolioFactory):
    '''
    Manages portfolio during simulation, and stays aware of the situation
    through the update() method.

    User strategies call it with a dictionnnary of detected opportunities (i.e.
    buy or sell signals).  Then the optimize function computes assets
    allocation, returning a dictionnary of symbols with their weigths or amount
    to reallocate.

    {'buy': zipline.Positions}    __________________________      _____________
    signals                   --> |                         | -> |            |
                                  | trade_signals_handler() |    | optimize() |
    orders  {'google': 34}    <-- |_________________________| <- |____________|

    This is abstract class, inheretid class will eventally overwrite optmize()
    to expose their own asset allocation strategy.
    '''
    def initialize(configuration):
        '''
        This method runs only once and before any trade. Use it to initialize
        your strategy.
        '''

    def optimize(self, date, to_buy, to_sell, parameters):
        '''
        Specifies the portfolio's allocation strategy. Available:
        self.portfolio    : zipline portfolio object
        self.max_assets   : maximum assets the portfolio can have at a time
        self.max_weigths  : maximum weigth for an asset can have in the portfolio
        _____________________________________________
        Parameters
            date: datetime.timestamp
                Date signals were emitted
            to_buy: zipline.Position
                Dict-like positions taged as "buy opportunity" by the algorithm
            to_sell: zipline.Position
                Same but for sell signals
            parameters: dict(...)
                Custom user parameters
                You can fill it through the configuration of using in your
                algorithm "self.manager.advise(key=value)"
        _____________________________________________
        Return:
            allocations: dict(...)
                Symbols with their
                    -> weigths -> for buy: according the whole portfolio value   (must be floats)
                               -> for sell: according total symbol position in portfolio
                    -> amount: number of stocks to process (must be ints)
            e_ret: float
                Expected return
            e_risk: float
                Expected risk
        '''
```

---
##### last modified on: March 19, 2014
---
