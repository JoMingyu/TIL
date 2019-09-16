# contextlib.suppress
`suppress`는 context manager로, with블럭 내에서 인자로 넘긴 클래스에 대한 exception 발생을 무시한다.

```python
from contextlib import suppress

with suppress(KeyError):
    {'a': 1}['b']
```

아래처럼 exception 발생 시 pass하는 형태의 try-except보다 나을 수 있다.

```python
try:
    {'a': 1}['b']
except KeyError:
    pass
```
