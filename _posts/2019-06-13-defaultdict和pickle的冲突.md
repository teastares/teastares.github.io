---
title: defaultdict和pickle的冲突
published: true
---

collections.defaultdict和pickle竟然有冲突，这两个可都是Python的内置模块。

找到了两个解决方案：

# 解决方案一：改用dill

参考[stackoverflow](https://stackoverflow.com/questions/35603979/pickling-defaultdict-with-lambda)上的回答，在冲突发生时，可以不用pickle，改用dill。

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

# 解决方案二：构造一个DefaultDict类

multiprocess使用了pickle管理多进程的内存，没办法，必须用pickle的场景，只好自己构造一个DefaultDict了。

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