---
title: the conflict between defaultdict and pickle
published: true
---

It is very ridiculous to find the conflict between defaultdict and pickle, both of them are the build-in packages in Python.

I found two solutions to solve the conflict.

# Solution 1: use "dill"

Refer to the answer from [stackoverflow](https://stackoverflow.com/questions/35603979/pickling-defaultdict-with-lambda), we can use dill instead of pickle.

```
# From pickle to dill

>>> import dill
>>> from collections import defaultdict
>>> pos = defaultdict(lambda: 0)
>>> neg = defaultdict(lambda: 0)
>>> countdata = (pos,neg)
>>> _countdata = dill.loads(dill.dumps(countdata))
>>> _countdata
(defaultdict(<function <lambda> at 0x10917f7d0>, {}), defaultdict(<function <lambda> at 0x10917f8c0>, {}))
>>>
>>> # now dump countdata to a file 
>>> with open('data.pkl', 'wb') as f:
...     dill.dump(countdata, f)
...
>>>
```

# Solution 2：Design a new DefaultDict class

In some situations we must use pickle, e.g. you have to use multiprocess and multiprocess use pickle to manage the memory between processes.

So we have to design a new DefaultDict class.

```
class DefaultDict(dict):
    """
    default dict created by Teast Ares.
    """
    def set_default_item(self, default_item):
        self.default_item = default_item
        
    def __missing__(self, key):
        self[key] = self.default_item
        return self.default_item
```