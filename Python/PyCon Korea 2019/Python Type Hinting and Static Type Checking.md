# Python Type Hinting and Static Type Checking
타입 힌팅에 관한 발표. 난이도는 초급이지만 새로 알게 된 것들이 좀 있다.

## Tuple[*types]
Tuple은 인덱스별로 타입을 먹일 수 있다.

```python
from typing import Tuple

_: Tuple[str, int, bool] = ('PlanB', 0, True)
```

## NewType
NewType 클래스를 통해 새로운 타입을 만들 수 있다.

```python
from typing import Union, List, NewType

Numeric = NewType('Numeric', Union[int, float])
NumericList = NewType('NumericList', List[Numeric])
```

## TypeVar
TypeVar 클래스를 잘 써먹으면 Generic이 가능하다. 아래는 간단한 example.

```python
from typing import List, TypeVar

T = TypeVar('T')


def get_last(data: List[T]) -> T:
    return data[-1]
```

아래는 클래스에 Generic을 쓰는 예.

```python
from typing import Generic, TypeVar

T = TypeVar('T')


class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: List[T] = []
    
    def push(self, item: T) -> None:
        self.items.append(item)
    
    def pop(self) -> T:
        return self.items.pop()


stack = Stack[int]()
stack.push(100)
stack.push(99)
```

## Callable에 타입 힌팅
아래처럼 Callable에 타입힌팅할 수 있다.

```python
from typing import Callable


def decorate_something(f: Callable[[int, int], str]):
    ...
```
