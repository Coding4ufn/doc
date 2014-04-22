---
sidebar_current: "api-data-sources"
---

# Data Sources

They're in charge to fetch or generate data to be processed by strategies.
You can use them to feed basic quotes or develop more sophisticated inputs you
will have access in the algorithm (like technical signals, data clean up,
pre-computation, ...)


## API

Unlike other modules, Data sources don't need to inherit from any `intuition`
abstract factory. Instead, it will be used "as is" by `intuition.api.datafeed.HybridDataFactory`.

```python
class MySource(object):
  '''
  Data sources yield events, processed by the algorithms, from a data descriptor
  providing dates, trading universe (i.e. tickers, market, random, ...) and
  custom parameters.
  '''
```

Currently, you have to implement an `__init__` method with the following
signature.

```python
  def __init__(self, sids, properties):
    '''
    - `sids` are a list of securities we need data for.

    - `properties` holds user configuration loaded at startup time. Use it to
      pass any parameters you would need for initialization.
    '''
    pass
```

`get_data()` is where we actually return the data. Backtest sources should
return the complete session dataset, where live sources need to provide the
current snapshot.

```python
  def get_data(self, sids[, start ,end]):
    '''
    - For a source used as backtest, start and end are the first and last
      trading days configured.

    - Returns a pandas.DataFrame or pandas.Panel used as trading events.
      DataFactory can process the following schemes

          | goog | aapl              | open | high | volume
    -------------------   or   ----------------------------
    14/03 | 34.5 | 345         14/03 | 34.3 | 37.8 | 120056
    15/03 | 34.9 | 344         15/03 | 36.3 | 36.9 | 103565


      aapl / ..  /  ..  /  ..
     goog / ..  /  ..  /  ..
    -----| open | high | close
    14/03| ____ | ____ | _____
    15/03|      |      |


    - Live data sources  should return a snapshot of data (i.e. at the current,
      frozen time.) Somethink like this (random) dataframe:

         | usd/gbp | eur/usd
    -------------------------
    rate |  0.658  |  1.658
    bid  |  0.659  |  1.358
    ask  |  0.653  |  1.023

    '''
    pass
```

Finally we need to normalize our data input. The `mapping` function below can
also be a good place to add additional fields computed from the ones we
downloaded.

```python
  @property
  def mapping(self):
    '''
    Sanitize your data input with correct fields and filters. You need at least
    to expose the following fields.
    '''
    return {
        'dt': (lambda x: x, 'dt'),
        'sid': (lambda x: x, 'sid'),
        'price': (float, 'price'),
        'normalized_price': (lambda x: x / self.top_past_price, 'price'),
        'volume': (int, 'volume'),
    }
```


## Usage

Like in the [algorithm section](/articles/api/algorithms.html), we will create a
minimal, standalone, backtest data source implementation.

```python
import random
import pytz
import pandas as pd

class DataGenerator(object):

    def __init__(self, sids, properties):
        pass

    def get_data(self, sids, start, end):
        index = pd.date_range(start, end, tz=pytz.utc)
        return pd.DataFrame({sid: [random.random()] * len(index)
            for sid in sids}, index=index)
```

A default `mapping()` method is implemented and it will work just fine with
this kind of dataframe. If you want to learn more about this mapping thing
anyway, check [the examples of Insights](/articles/insights/data-sources.html).

Now let's make it an `intuition` compliant data source that you can even use in
other finance projects.

```python
from intuition.api.datafeed import HybridDataFactory
from intuition.data.universe import Market

# Market informations. Here we trade 5 stocks from the cac40
market = Market()
market.parse_universe_description('stocks:paris:cac40,5')
data_source = HybridDataFactory(
    universe=market,
    index=pd.date_range('2012/01/01', '2012/06/01', tz=pytz.utc),
    backtest=DataGenerator
)
```

And now we can use our data generator which is indeed a python generator.

```python
for event in data_source.raw_data:
    # [2014-04-16 14:01] DEBUG: intuition.api.datafeed: {"event": "--> next tick 2013-01-01 07:00:00+00:00"}
    print event
    # {'volume': 1000, 'price': 0.039295113592690223, 'dt': Timestamp('2013-01-01 07:00:00+0000', tz='UTC'), 'sid': 'lg.pa'}
```

## Live Sources

There would be the following differences :

* The index provided to `HybridDataFactory` should of course end at a future date
* `get_data(self, sids) would return a snapshot` as mentioned
* The generator will sleep until the next event date. This is actually also the
  case for backtest sources but since dates are in the past, we never have to
  wait.

And that's all ! Under the hood live and backtest mode share pretty much the
same code. This way you can even begin a trading session as a backtest, to
compute moving averages from the previous month for example, and seamlessly
switch to live mode without anything particular to do.

Please notice, though, it could introduce some middleware issues (you don't
want to receive mobile notfications while backtesting). Although some
securities are implemented, give a look at the [middleware
section](/articles/api/plugins.html) to see how you can simply handle it.


## Frequency management

Until now we used the default behavior : daily bars. However you can provide a
`frequency` parameter in the `data` section of your configuration and tweak the
generator.

```yaml
# Add these lines to your configuration
data:
  # 3 is the number of hours but floats are fine
  frequency: 3
```

With that only, your source will yield an event every 3 hours after the market
opening and until its close.

Unfortunately most of the sources we can grab for free provide daily bars at
best for backtests. If `intuition` can't find available data, it will try its
best to extrapolate reliable values, suited for study.

This is a really new feature and currently it only uses the previous quote.
Much more powerful technics are expected soon.

---
##### last modified on: March 19, 2014
---
