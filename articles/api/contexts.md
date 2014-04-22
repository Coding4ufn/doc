---
sidebar_current: "api-contexts"
---

# Contexts

*intuition* offers various ways to customize its behavior. The downside is
configurations could grow really complex and hard to maintain.

Enter *context loaders*. They provide different ways to load the whole
configuration, from algorithm parameters to trading timeline, and harmonize the
process with smart default parameters and sain checkers.

This way it becomes much easier to find the adequate loader to suit your
preferences of specific integration. For example, while flat files are ok for
local development, I needed a remote solution for the web dashboard hosted on
[intuition.io](https://intuition.io).


## API

Once again, to keep stuff consistent, the implementation follows the same logic
as other *intuition* building blocks.

A new *context loader* inherits from an abstract ContextFactory. This parent
essentially parses user input and normalizes inputs.

```python
from intuition.api.context import ContextFactory

class MyContextLoader(ContextFactory):
    '''
    Context loaders gives to Intuition everything it needs to know about user
    configuration. It also provides some methods to make the setup process
    easier.
    '''
```

As usual we begin to interpret user input to setup the process. This input have
the same format as an url, i.e. :

`<context.module>://<network:info>/path/to/config?keys=values&...`

```python
    def initialize(self, storage):
        '''
        The storage dictionnary should contain everything you need to
        initialize your Context.
        Intuition parses :
          `--context insights.FileContext://localhost/somewhere/config.json?key=value`
        into :
          storage = {
              'uri': 'localhost',
              'path': ['somewhere', 'config.json'],
              'params': {'key': 'value'},
            }
        '''
        self.config_url = storage['uri']
        self.timeout = storage['params']['timeout']
```

Once initialized, our class must properly load a correct configuration
*intuition* will use. A *correct* configuration is actually not really
restrictive, [as shows the schema checker
here](https://github.com/intuition-io/intuition/blob/master/intuition/constants.py).

Here is an example :

```json
{
  'start': '2013/01/01',
  'end': '2014/01/01',
  'universe': 'forex',
  'modules': {
    'algorithm': 'insights.algorithms.signals.RSIWithM',
    'manager': 'insights.managers.gmv.GlobalMinimumVariance',
    'backtest': 'insights.sources.backtest.database.AugmentedRethinkdb'
  },
  'manager': {
    'cash': 10000
  }
}
```

```python
def load(self):
    '''
    This function must return a configuration dictionnary for intuition.
    '''
    data = requests.get(self.config_url, timeout=self.timeout)
    if data.ok:
        return data.json()
    else:
       raise TerribleFatalError('Got unexpected data: ' + data.content)
```

---
##### last modified on: March 19, 2014
---
