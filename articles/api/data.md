---
sidebar_current: "api-data"
---

# Financial Data

The `intuition.data` module groups various routines that can fetch and
manipulate datasets. They are especially useful for [data sources](/articles/api/data-sources.html)
but you can use them as standalone pieces of code as well.

## Market

Its goal is to hold the worldwide market knowledge. Wether it's about stock
symbols or exchagne schedules, you should rely on it when you want to deal with
human-readable informations and automatic market details setup.

For example, it loads systematically the market scheme for convenient use.

```python
from intuition.data.universe import Market

market = Market()
print market.scheme['forex']['pairs']
print market.scheme['stocks']['paris']['timezone']
print market.scheme['stocks']['paris']['benchmark']
```

*intuition* heavily uses it to parse user-defined configuration.

```python
user_universe = 'forex,15'
market.parse_universe_description(user_universe)
print market.sids        # ['gbp/chf', 'usd/sek', ...]
print len(market.sids)   # 15

# Use ':' to walk market structure
user_universe = 'stocks:paris:cac40,15'
market.parse_universe_description(user_universe)
print market.sids        # ['ead.pa', 'vk.pa', ...]
print market.benchmark   # 'fchi'
print market.timezone    # 'Europe/Paris'
```

Note on the roadmap: zipline trading calendars and other markets will be added
here.


## Quandl

This class wraps API calls to [Quandl](http://quandl.com/) so we can get access to its
> over 9 000 000 financial, economic and social datasets.

```python
from intuition.data.quandl imort Quandl

# An effort is made to use consistent symbols with the rest of data modules.
symbols = ['su.pa', 'gle.pa']

qdl = DataQuandl(quandl_key='ebzvrzvzvz')
# Alternately for authentification, you can:
#   - store the API key into the env as QUANDL_API_KEY
#   - use 'authtoken' keyword with the <fetch> call

# Get OHLCV for our symbols (and most of the time 'adjusted_close' as well)
data = qdl.fetch(symbols, start='2002/01/01', end='2012/01/01')
# Apart from <start> and <end>, all of the official parameters are supported.
# Check the python package : https://github.com/quandl/Python

data['su.pa'].head()
```


## Forex

`intuition.data.forex` currently provides an access in real time to [26 pairs
freely available on truefx](http://truefx.com/) (note this pairs are listed in
~/.intuition/data/market.yml). Without an account you'll still have access up
to 10 pairs.

```python
from intuition.data.forex import TrueFX

# If you plan to always use the same pairs, pass the list here
fx = TrueFX(credentials='login:pass', pairs=[])
# Similar to quandl, you can pass the credentials through the TRUEFX_API
# environment variable
fx.connect()

# If you set the pairs in the constructor, you can skip the argument
print fx.query_rates(['eur/usd', 'aur/aud', 'eur/nzd'])
```


## Yahoo Finance


## Google Finance



---
##### last modified on: April 9, 2014
---
