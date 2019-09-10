# dacite to pydantic에서 타입 관련 문제
dacite도 타입 검사를 한다. 데이터클래스에 값을 넣어주기만 하는 게 아니라는 것. 그래서 pydantic만의 타입으로 힌팅된 변수가 있다면, dacite 단에서는 이걸 알아듣지 못하니 `isinstance`에서 걸려버린다.

```python
from dacite import from_dict
from pydantic import conint
from pydantic.dataclasses import dataclass


@dataclass
class User:
    age: conint(gt=0)
    name: str


User(age=3, name='planb')  # 문제 없음!
from_dict(data_class=User, data={'age': 3, 'name': 'planb'})
# dacite.exceptions.WrongTypeError: wrong type for field "age" - should be "ConstrainedIntValue" instead of "int"
```

from_dict가 받는 인자 중에 Config가 있고, 소스코드에서 이걸 찾아보니 type checking을 끄는 옵션이 있었다.

```python
from dacite import Config, from_dict
from pydantic import conint
from pydantic.dataclasses import dataclass


@dataclass
class User:
    age: conint(gt=0)
    name: str

from_dict(data_class=User, data={'age': 3, 'name': 'planb'}, config=Config(check_types=False))
```
