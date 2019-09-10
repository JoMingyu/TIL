# python_requires
setup 함수에 python_requires 인자를 전달해 패키지를 사용할 수 있는 파이썬 버전을 강제할 수 있다.

```python
from setuptools import setup

setup(
    name="my_package_name",
    python_requires='>=2.6, !=3.0.*, !=3.1.*, !=3.2.*, <4',
    ...
)
```
