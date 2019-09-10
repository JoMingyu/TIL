# find_packages
setup 함수의 인자로 전달하는 `packages`에 패키지 이름들을 직접 작성하는 건 옛날 방식이라고 한다. `find_packages` 함수는 현재 path 기준으로 패키지를 뒤져서, 패키지들의 목록을 만들어 준다. 포함시키지 말아야 할 패키지를 인자로 전달한다거나 하는 유연성도 제공되어 있다. Flask Large Application Example에서 써봤다.

```python
from setuptools import find_packages

print(find_packages())
# ['app', 'config', 'constants', 'tests', 'app.decorators', 'app.extensions', 'app.hooks', 'app.misc', 'app.models', 'app.views', 'app.views.sample', 'tests.app', 'tests.app.context', 'tests.app.decorators', 'tests.app.hooks']

print(find_packages(exclude=('tests',)))
# ['app', 'config', 'constants', 'app.decorators', 'app.extensions', 'app.hooks', 'app.misc', 'app.models', 'app.views', 'app.views.sample', 'tests.app', 'tests.app.context', 'tests.app.decorators', 'tests.app.hooks']

print(find_packages(exclude=('tests*',)))
# ['app', 'config', 'constants', 'app.decorators', 'app.extensions', 'app.hooks', 'app.misc', 'app.models', 'app.views', 'app.views.sample']

print(find_packages(include=('app*',)))
# ['app', 'app.decorators', 'app.extensions', 'app.hooks', 'app.misc', 'app.models', 'app.views', 'app.views.sample']
```

왜 필요한가 싶었는데, 하위 패키지를 포함시키려면 그 상위 패키지가 이미 적혀있더라도 따로 적어줘야 하기 때문이다.(`app`을 적어 둔다고 `app/views`가 포함되지 않음)
