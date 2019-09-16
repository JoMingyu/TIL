# HTTPStatus
HTTPStatus는 HTTP Status Code들이 정리되어 있는 Enum이다.

```python
from http import HTTPStatus
print(HTTPStatus.OK)
# <HTTPStatus.OK: 200>
print(HTTPStatus.OK == 200)
# True
print(http.HTTPStatus.OK.value)
# 200
print(HTTPStatus.OK.phrase)
# 'OK'
print(HTTPStatus.OK.description)
# 'Request fulfilled, document follows'
print(list(HTTPStatus))
# [<HTTPStatus.CONTINUE: 100>, <HTTPStatus.SWITCHING_PROTOCOLS: 101>, ...]
```
