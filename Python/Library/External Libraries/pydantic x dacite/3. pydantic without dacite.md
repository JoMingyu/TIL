# pydantic without dacite
아니 pydantic dataclass에 dictionary 주면 그냥 되지 않을까 ㅋㅋ?

```python
from pydantic.dataclasses import dataclass


@dataclass
class Dummy:
    a: int
    b: str


print(Dummy(**{'a': 3, 'b': 'h'}))
# Dummy(a=3, b='h')
```

```python
from pydantic.dataclasses import dataclass


@dataclass
class Inner:
    a: int
    b: int


@dataclass
class Something:
    inner: Inner
    b: str


print(Something(**{'inner': {'a': 1, 'b': 3}, 'b': 'qwe'}))
# Something(inner=Inner(a=1, b=3), b='qwe')
```

ㄴㅇㄱ 그럼 그냥 빌트인 dataclass 호출하듯 inner 인자로 dictionary를 넣어도 되는 거 아님?

```python
print(Something(inner={'a': 1, 'b': 3}, b='qwe'))
# Something(inner=Inner(a=1, b=3), b='qwe')
```

pydantic.dataclass가 init에서 필드의 타입이 pydantic.dataclass고 인풋이 dict면 convert해준다거나 하는 게 있나? 코드를 보는 게 좋을 것 같다. 복잡해서 한 30분 살펴보니까, 인자의 값이 dictionary면 아래의 `_validate_dataclass` 함수를 호출하게 되어 있다.

```python
def _validate_dataclass(cls: Type['DataclassType'], v: Any) -> 'DataclassType':
    if isinstance(v, cls):
        return v
    elif isinstance(v, (list, tuple)):
        return cls(*v)
    elif isinstance(v, dict):
        return cls(**v)
    else:
        raise DataclassTypeError(class_name=cls.__name__)
```

결론은 dacite 없이도 잘 할 수 있다 ㅡㅡ
