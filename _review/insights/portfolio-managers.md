---
sidebar_current: "insights-portfolio-managers"
---

Portfolio Managers Library
==========================

Here is a list of the portfolio managers currently implemented, with a short
description and the parameters exposed (values are defaults)

* Global Minimum Variance
> Computes from data history a suitable compromise between risks and returns.
```python
parameters = {window: 40, only_full: True}
```

* OLMAR
> On-Line Portfolio Moving Average Reversion
```python
parameters = {eps: 1}
```

* Constant
> Buys and sells a constant defined amount
```python
parameters = {buy_amount: 100, sell_amount: 100, scale: {GOOG: 2, AAPL: 0.1}}
```

* Fair
> Dispatch equals weigths for buy signals and give up everything on sell ones
```python
parameters = {}
```

* Optimal Frontier
> Computes with R the efficient frontier and pick up the optimize point on it
```python
parameters = {per_sell: 1.0, max_weigths: 0.2, window: 50, refresh: 1, only_full: True}
```

* Black Litterman (work in progress)
> This algorithm performs a Black-Litterman portfolio construction. The
> framework is built on the classical mean-variance approach, but allows the
> investor to specify views about the over- or under- performance of various
> assets.  Uses ideas from the Global Minimum Variance Portfolio algorithm
> posted on Quantopian. Ideas also adopted from
> www.quantandfinancial.com/2013/08/portfolio-optimization-ii-black.html.
```python
parameters = {window: 255, refresh: 10, only_full: True}
```

---
##### last modified on: March 19, 2014
---
