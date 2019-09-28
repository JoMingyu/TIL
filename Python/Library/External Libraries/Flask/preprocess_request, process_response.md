# preprocess_request, process_response
각각 실제로 요청이 디스패치되기 전과 후에 flask application에 대해 이 메소드가 호출된다고 한다. 정말 그런지 테스트 먼저 해 보자!

```python
from flask import Flask


class CustomFlask(Flask):
    def preprocess_request(self):
        print('preprocess_request!')
        super(CustomFlask, self).preprocess_request()

    def process_response(self, *args, **kwargs):
        print('process_response!')
        return super(CustomFlask, self).process_response(*args, **kwargs)


app = CustomFlask(__name__)


@app.route('/')
def index():
    print('in request!')
    return 'hi'


app.run(debug=True)
```

분기 흐름은 preprocess_request, before_request 친구들, view function, process_request, after_request 순서가 되는구나! request hook을 테스트할 때 좋을 듯. flask application 만들고, hook 달아놓고, context 안에서 preprocess_request나 process_response 호출하면 되니까.
