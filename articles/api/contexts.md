---
sidebar_current: "api-contexts"
---

# Contexts

```python
from intuition.api.context import ContextFactory

class FileContext(ContextFactory):

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

    def load(self):
        '''
        This function must return a configuration dictionnary for intuition of
        that kind :

        return {
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
        '''
```

---
##### last modified on: March 19, 2014
---
