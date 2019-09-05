# pydantic x dacite
pydantic은 좋다! 근데 API 입장에서 dictionary로 오는 데이터를 pydantic으로 넘겨줘야 하는데, pydantic은 기본적으로 dataclass 컨셉이라 좀 어렵다. dataclass가 간단하다면 그냥 argument unpacking해서 던져줄텐데, depth가 깊다면 써먹을 수가 없다.

```python
from dataclasses import dataclass

@dataclass
class Item:
    name: str
    weight: int

Item(**{'name': 'Sword', 'weight': 1})
# flat하다면 argument unpacking해도 상관 없음


@dataclass
class ItemSet:
    item: Item
    quantity: int

ItemSet(**{'item': {'name': 'sword', 'weight': 1}, 'quantity': 3})
# ItemSet(item={'name': 'sword', 'weight': 1}, quantity=3)로 돼버림
# ItemSet(item=Item(name='Sword', weight=1), quantity=3) <- 이렇게 돼야 함
# 안됨 에바임
```

게다가 requested json이 복잡해지면 flat한 구조를 이어가기 힘들어서 이런 문제를 무시할 수가 없다. dacite라고 dictionary를 dataclass로 잘 주입해주는 친구가 있는데, 같이 쓰면 좋을 것 같다.

## dacite
dacite의 usage는 이런 식.

```python
from dataclasses import dataclass
from dacite import from_dict


@dataclass
class A:
    x: str
    y: int


@dataclass
class B:
    a: A


data = {
    'a': {
        'x': 'test',
        'y': 1,
    }
}

result = from_dict(data_class=B, data=data)
# result: B(a=A(x='test', y=1))
```

합치면?

```python
from dacite import from_dict
from pydantic.dataclasses import dataclass

@dataclass
class Item:
    name: str
    weight: int


@dataclass
class ItemSet:
    item: Item
    quantity: int


data_dict = {
    'item': {
        'name': 'sword',
        'weight': 1
    },
    'quantity': 3
}

result = from_dict(data_class=ItemSet, data=data_dict)
# result: ItemSet(item=Item(name='sword', weight=1), quantity=3)
```
