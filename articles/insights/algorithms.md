---
sidebar_current: "insights-algorithms"
---

Algorithms Library
==================

Here is a list of the algorithms currently implemented, with a short
description and the configuration exposed (values are defaults)

Most of them take also boolean parameters 'save' (portfolio metrics storage in
database), 'interactive' (manual commands through redis), 'notify' (andorid
notifications with [Push Bullet](pushbullet.com)) and 'commission' (the
transaction cost).

* Buy And Hold
> Buy every sids on the given day, regularly or always (start_day = -1), and
> until the end
```python
properties = {start_day: 2, rate: -1}
```

* Stochastic Gradient Method
> Randomly chooses training data, gradually decrease the learning rate, and
> penalize data points which deviate significantly from what's predicted. Here
> We used an average SGD method that is tested to outperform if we simply pick
> the last predictor value trained after certain iterations.
```python
properties = {refresh: 1, window: 60, gradient_iterations: 5}
```

* Regular Rebalance
> Reconsidere the portfolio allocation every <refresh> periods,
> providing to the portfolio strategy <window_length> days of quote data.
```python
properties = {refresh: 10, window: 40}
```

* Random
> For each sid, at each event, randomly choose to sell or buy
```python
properties = {buy_trigger: 0.5 < x < 1, sell_trigger: 0 < x < 0.5}
```

* Standard Deviation based (Always loose, there's something wrong !)
> Open Long Position if current price is larger than the 9 day volume weighted
> average plus 60% of the standard deviation Then positions are monitored with
> takeprofit and stoploss limits.  Plus, if we loose more than plug *
> starting_cash, we sell and stop trading.
```python
properties = {stddev: 9, vwap_window: 5, plug: 0.7}
```

* Auto adjusting stop loss
> The goal behind this algorithm was to invest in a broad range of stocks,
> eliminate the ones that aren't doing well, and "double down" on the ones that
> are.  The stop loss price is set as a function of the rate of return, and
> trails the current price so as to lock in a profit in the event that the
> price of a particular security starts dropping
```python
properties = {}
```

* Volume weight average price
> For each frame, it finds the price and calculates the volume-weighted average
> price.  If the price is moving quickly, and we have not exceeded our position
> limits, it executes the order and updates our position.
```python
properties = {window: 3, buy_trigger: -5, sell_trigger: 5}
```

* Momentum
> Buy when price > moving average and vice versa, but only if the stock
> notional is between max_weigth and max_expose
```python
properties = {window: 3, max_weight: 0.2, max_exposure: max_weight}
```

* Dual Moving Average
> Buys once its short moving average crosses its long moving average
> (indicating upwards momentum) and sells its shares once the averages cross
> again (indicating downwards momentum).
```python
properties = {long_window: 30, ma_rate: 0.5, short_window: ma_rate * long_window, threshold: 0}
```

---
##### last modified on: March 19, 2014
---
