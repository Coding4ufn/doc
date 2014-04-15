---
sidebar_current: "api-data-sources"
---

# Data Sources


## Implementation

```python
class MySource(object):
  '''
  Data sources yield events, processed by the algorithms, from a data descriptor
  providing dates, trading universe (i.e. tickers, market, random, ...) and
  custom parameters.
  '''

  def __init__(self, sids, properties):
    '''
    - `sids` are a list of securities we need data for.

    - `properties` holds user configuration loaded at startup time. Use it to
      pass any parameters you would need for initialization.
    '''
    pass

  def get_data(self):
    '''
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
        'volume': (int, 'volume'),
    }
```


## Usage

Let's create a minimal implementation:

```python
```

---
##### last modified on: March 19, 2014
---
