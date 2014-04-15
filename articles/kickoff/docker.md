---
sidebar_current: "kickoff-docker"
---

# Intuition with Docker

## Development environment

If you don't know docker, learn more on the [official getting started
guide](https://www.docker.io/gettingstarted/).

Once installed, fire up an Intuition development environment :

```console
$ docker run -i -t \
$     -e LANG=fr_FR.UTF-8 \  # Useful for timezone customization (default is New York)
$     hivetech/intuition bash
```

Everything should be setup (you can check it with `intuition --help`), so you
can [learn how to use Intuition](/articles/intuition/command-line.html) and
[code your first algorithm](/articles/api).


## Encapsulated app

The Intuition container is also a smart way to run a self-contained instance of
the project. In the following example, I stored the configuration as a
[mongodb](https://github.com/intuition-io/insights/blob/develop/insights/contexts/mongodb.py)
document (called `bt-yahoo`). Since I use the [AugmentedRethinkdb backtest data
source](https://github.com/intuition-io/insights/blob/develop/insights/sources/backtest/database.py),
I also need to give the environment my [Quandl](http://quandl.com) API key.

```console
$   docker run \
$     -e LANG=fr_FR.UTF-8 \
$     -e LOG=info \
$     -e DB_HOST=172.17.0.4 \
$     -e QUANDL_API_KEY=$QUANDL_API_KEY \
$     hivetech/intuition intuition --context insights.contexts.mongodb.MongodbContext://192.168.0.19:27017/intuition/contexts/bt-yahoo --id chuck --bot --showlog
```

Et voilà !


-------
##### last modified on: March 23, 2013
-------
