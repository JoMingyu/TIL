# try on yield
context manager에서 yield를 try로 감싸고 finally에서 리소스 release해주는 게 국룰인가 보다.

```python
from contextlib import contextmanager

@contextmanager
def managed_resource(*args, **kwds):
    resource = acquire_resource(*args, **kwds)
    try:
        yield resource
    finally:
        release_resource(resource)
```
