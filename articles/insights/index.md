---
sidebar_current: "insights"
---

# Insights

[![Latest Version](https://pypip.in/v/insights/badge.png)](https://pypi.python.org/pypi/insights/)
[![Build Status](https://travis-ci.org/hackliff/insights.png?branch=master)](https://travis-ci.org/hackliff/insights)
[![Coverage Status](https://coveralls.io/repos/hackliff/insights/badge.png)](https://coveralls.io/r/hackliff/insights)
[![Code Health](https://landscape.io/github/hackliff/insights/master/landscape.png)](https://landscape.io/github/hackliff/insights/master)
[![Requirements Status](https://requires.io/github/hackliff/insights/requirements.png?branch=master)](https://requires.io/github/hackliff/insights/requirements/?branch=master)
[![License](https://pypip.in/license/insights/badge.png)](https://pypi.python.org/pypi/insights/)

> Plug-and-play building blocks for modern quants

Quantitative algorithms, portfolio managers data sources, contexts and
middlewares for [Intuition][1], contributed by the community.

## Installation

```console
$ (sudo) apt-get install -y libssl-dev libreadline-dev
$ git clone --depth 1 --branch develop https://github.com/hackliff/insights.git
$ cd insights && python setup.py install
```

## Extra features

### [TA-Lib](http://ta-lib.org/)

From the [Github repository](https://github.com/mrjbq7/ta-lib) :

> TA-Lib is widely used by trading software developers requiring to perform
> technical analysis of financial market data.

> * Includes 150+ indicators such as ADX, MACD, RSI, Stochastic, Bollinger
>   Bands, etc.
> * Candlestick pattern recognition
> * Open-source API for C/C++, Java, Perl, Python and 100% Managed .NET

To make it available in your algorithm, you need to install it from source.

```console
$ # Package dependencies
$ sudo apt-get install libopenblas-dev liblapack-dev gfortran

$ # Ta-Lib itself
$ wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$ tar -xvzf ta-lib-0.4.0-src.tar.gz
$ cd ta-lib/
$ ./configure --prefix=/usr
$ make
$ sudo make install

$ # Python wrapper
$ (sudo) pip install --use-mirrors TA-Lib==0.4.8
```

You can browse `algorithms.signals.RSIWithMA` algorithm to read how to use it.


### R Support

`R` is a powerful, widely used, language for statistical analysis and financial
computation. You will need it for the `Markowitz portfolio manager` and to
generate live trading reports.

Installation is a bit heavy but really easy :

```console
$ # Install R
$ sudo apt-get install r-base
$ # Install R libraries
$ wget -qO- http://bit.ly/L39jeY | R --no-save
```


### Report plugin

This plugin sends right in your mailbox stocks analysis when your algorithm
detected some opportunities.
You will need however R installed and a working [LaTex] environment.

```console
$ # The heavy work is for the computer
$ sudo apt-get install texlive
```


[1]: https://github.com/hackliff/intuition

---
##### last modified on: March 19, 2014
---
