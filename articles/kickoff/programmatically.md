---
sidebar_current: "kickoff-docker"
---

# Intuition as a library

Sometimes the plug-and-play, modular, architecture of the project isn't enough.
Especially if you plan to integrate `intuition` along other applications. An
other usecase would be to compute your own analyze at the end of a backtest.

The higher level of abstraction is pretty simple and I tried my best to make it
readable. [Check it out here](https://github.com/intuition-io/intuition/blob/develop/intuition/cli.py).

The following code provides a minimal implementation.
The [context modules](/articles/api/contexts.html) makes configuration easy to
deal with but, for the sake of clarity, I don't use them below.

```python
import pytz
import pandas as pd
from intuition.core.engine import Simulation
from intuition.api.datafeed import HybridDataFactory

modules = {
    "algorithm": "insights.algorithms.signals.RSIWithMA",
    "manager": "insights.managers.gmv.GlobalMinimumVariance",
    "backtest": "insights.sources.backtest.database.AugmentedRethinkdb"
}

strategy = {
    "algorithm": {"rsi_period": 14},
    "manager": {"cash": 20000},
}

# This object handles the market knowledge
market = universe.Market()
market.parse_universe_description('forex,15')

# This timeline suggests a backtest, we will learn about live traing later but
# it works the same way
trading_dates = pd.date_range('2013/01/10', '2014/02/01', tz=pytz.utc)

simulation = Simulation()
simulation.configure_environment(
    last_trade=trading_dates[-1],
    benchmark=market.benchmark,
    timezone=market.timezone)

simulation.build(identity='gekko', modules, strategy)

data_source = HybridDataFactory(
    universe=market,
    index=trading_dates,
    backtest=modules['backtest']
)

# Done with preparation, run the traiding session !
analyzes = simulation(data_source, auto=True)

# Explore the analyzes object
analyzes.build_report(show=True)
print analyzes.overall_metrics('one_month')
print analyzes.results.tail()
```

The design of `intuition`, actually, allows much more liberty. Financial
routines, portfolio allocation strategies, data sources, ... I try to keep
everything as reusable building blocks for financial development. Feel free to
use them and imagine whole new applications.

If you're interested into hacking further, head up to the [API section](/articles/api)


-------
##### last modified on: April 5, 2013
-------
