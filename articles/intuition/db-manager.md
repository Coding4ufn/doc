---
sidebar_current: "intuition-db-manager"
---

# Intuition database manager

This convenient script helps you to store and manage quotes data. Specifically,
you'll be able to download data from Quandl, store it in database and
eventually synchronize it later.

In its current state, this is especially usefull to use along the
`insights.sources.backtest.AugmentedRethinkdb` data source. Also, the only
database supported for now is [Rethinkdb](http://rethinkdb.com).


## Installation

The script is installed with
[insights](https://github.com/intuition-io/insights). If you haven't installed
it yet, [follow these steps](/articles/insights).

```console
$ intuition-db -h

info | - Intuition database manager
warn | Only supports cac40 and forex symbols
warn | Only supports rethinkdb backend
usage: intuition-db [-h] [-V] [-v] -u UNIVERSE [-s START] [-e END]
                    [-b BACKEND]
                    command

Intuition-db, quotes database manager

positional arguments:
  command               command [dl, sync]

optional arguments:
  -h, --help            show this help message and exit
  -V, --version         Print program version
  -v, --verbose         Print logs on stdout
  -u UNIVERSE, --universe UNIVERSE
                        market universe to evaluate
  -s START, --start START
                        start date for quotes
  -e END, --end END     end date for quotes
  -b BACKEND, --backend BACKEND
                        database backend to use for storage
```


## Download

The following command fetchs forex data from [Quandl](http://quandl.com) and
store it under the `quotes` table in a RethinkDB database.

```console
$ intuition-db dl --start 2012/01/01 --end 2012/12/20 --universe forex
```
`--start`, `--end` and `--universe` use the same mechanism as in a regular
*intuition* configuration. For example you could replace `--universe forex`
with `--universe stocks:paris:cac40,5`


## Synchronize

Say some weeks have passed and we want to update our  data available offline.

```console
$ intuition-db sync --universe forex
```

The script will detect the last entry date and complete the data until the most
recently available on Quandl.


---
##### last modified on: March 19, 2014
---
