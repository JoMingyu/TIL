# property deleter
property에는 setter만 있는 게 아니라 deleter도 있다. del할 때 호출됨.

```python
class M:
    def __init__(self):
        self._m = None

    @property
    def m(self):
        return self._m

    @m.setter
    def m(self, val):
        self._m = val

    @m.deleter
    def m(self):
        self._m = None

i = M()
i.m = 150
print(i.m) # 150
del i.m
print(i.m) # None
```
