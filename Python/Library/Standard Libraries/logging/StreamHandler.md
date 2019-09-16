# StreamHandler

StreamHandler는 sys.stdout, sys.stderr, file-like object 등 `write`와 `flush` 메소드를 지원하는 스트림으로 로깅 출력을 보낸다. 기본값으로는 `sys.stderr`가 사용된다고 함.

```python
import logging

logger = logging.getLogger('foo')
logger.setLevel(logging.INFO)

logger.info('hi')
# 별도의 출력이 없음

handler = logging.StreamHandler()
logger.addHandler(handler)
logger.info('hi')
# hi
```
