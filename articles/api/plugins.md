---
sidebar_current: "api-plugins"
---

Plugins
=======

Plugins are useful code snippets for adding features in a modular way.


Middlewares
-----------

Middlewares are special plugins. They expose a function which arguments are
intuition.api.TradingFactory attributes. Once this function registered at
algorithm initialization, *intuition* will call it after each trading event (In
the future, one will be able to specify more precisely when).

For example, the following code will print after each frame the current
portfolio.

```python
# Arguments names must be TradingFactory attributes or methods
def debug_portfolio(portfolio, datetime):
    print datetime, portfolio
```

Then You can use it from the ``initialize()`` method.

```python
class BuyAndHold(TradingFactory):

    def initialize(self, properties):
        self.use(debug_portfolio)

```

---
##### last modified on: April 3, 2014
---
