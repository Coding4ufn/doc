---
sidebar_current: "insights-data-sources"
---

Data Sources Library
====================

* Backtest.database
> Loads data from a [Rethinkdb][1] database.
> The universe can be any of yahoo-like symbols list, or `random[,n]` where n
> is an optional number of stocks.
> The `intuition-db` script is provided to feed some data to *Intuition*
```python
data_descriptor = {'database': 'quotes'}
```

* Backtest.quandl
> Fetch datasets from [Quandl][2].
> You can use yahoo-like symbols or market shortcuts (currently supported :
> cac40, nasdaq)

* Backtest.yahoo
> Fetch data from Yahoo finance.
> You can use yahoo-like symbols or market shortcuts (currently supported :
> cac40, nasdaq, nyse)
> YahooPriceSource allocates a dataframe of sids close prices, while
> YahooOHLCSource uses a panel of sid dataframes (with ohlc data)

* Backtest.csv
> Reads data from `.csv` files. For now, each file must be named {{ sid }}.csv
> (one sid data per file) and expose an header with at least a `Date` column
> for index. Those files must be located in ~/.intuition/data.
> The `--universe` option only understands comma separated list of csv files

* Live.forex
> Fetch real-time frex data from [TrueFX][3]
> You must subscribe to the service and export your logins like :
> `username:password`. Then you can provide a comma separated list of pairs or
> a market shortcut (forex[,n] where n is an optinal number of random pairs to
> trade)

* Live.equities
> Stream data from google finance.
> You can use the same universe options as backtest.yahoo

[2]: quandl.com
[3]: http://www.truefx.com/

---
##### last modified on: March 19, 2014
---
