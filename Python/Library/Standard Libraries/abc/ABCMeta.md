# ABCMeta
ABCMeta는 metaclass용 클래스로, 추상 클래스를 만들 때 메타클래스로 사용하면, 추상 메소드가 정의되지 않은 채 클래스가 인스턴스화되는 것을 막는다.

```python
from abc import ABCMeta, abstractmethod


class ParentWithoutABCMeta:
    @abstractmethod
    def a(self):
        pass


class ParentWithABCMeta(metaclass=ABCMeta):
    @abstractmethod
    def a(self):
        pass


class ChildWithoutABCMeta(ParentWithoutABCMeta):
    pass


class ChildWithABCMeta(ParentWithABCMeta):
    pass


ParentWithoutABCMeta()
ChildWithoutABCMeta()
# 둘 다 인스턴스화에 문제 없음

ParentWithABCMeta()
# TypeError: Can't instantiate abstract class ParentWithABCMeta with abstract methods a
ChildWithABCMeta()
# TypeError: Can't instantiate abstract class ParentWithABCMeta with abstract methods a
```
