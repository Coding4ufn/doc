---
sidebar_current: "insights-contexts"
---

# Contexts Library

## File

Simply provide the location of your yaml or json formated configuration file.

``--context file::path/to/file.{json,yaml,yml}``


## Mongodb

A configuration is stored as a [Mongodb][1] document. You must provide them
with an additional *id* information. Then you can use it with

``--context mongodb::{{ ip }}:{{ port }}/{{ doc_id }}``


## HTTP

The configuration is stored with [etcd][3] at v2/keys/{{ conf_id }}.

``--context etcd::{{ ip }}:{{ port }}/{{ conf_id }}``

To set a key / value :

```console
$ URL=http://{{ ip }}:{{ port }}/v2/keys/{{ conf_id }}
$ curl -L -XPUT $URL/universe -d value="nasdaq,5"
$ curl -L -XPUT $URL/{{ conf_id }}/algorithm/save -d value="true"
```


[1]: https://github.com/hackliff/intuition
[2]: mongodb.org
[3]: https://github.com/coreos/etcd

---
##### last modified on: March 19, 2014
---
