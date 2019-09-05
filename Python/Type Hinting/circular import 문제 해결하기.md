# circular import 문제 해결하기
타입 힌팅을 하다 보면 circular import가 생기는 경우가 있다. a, b 모듈이 있고 각각 어떤 클래스가 있을 때, 서로가 서로 것을 import해 힌팅하는 경우다. 아래와 같은 방식으로 해결할 수 있다.

```python
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from mod1 import Something
```
