# dataclass-json
용도 자체는 dacite의 역할인데, string case와 관련된 지원과 dataclass -> json 지원이 있다는 게 특징. 차라리 dacite의 상위호환인 듯?

```python
from dataclasses import dataclass
from dataclasses_json import dataclass_json, LetterCase

@dataclass_json
@dataclass
class SimpleExample:
    int_field: int

SimpleExample.from_json('{"int_field": 1}')  # SimpleExample(1)

@dataclass_json(letter_case=LetterCase.CAMEL)
@dataclass
class ConfiguredSimpleExample:
    int_field: int

ConfiguredSimpleExample(1).to_json()  # {"intField": 1}
ConfiguredSimpleExample.from_json({"intField": 1})  # ConfiguredSimpleExample(1)
```

데코레이터로 들어온 클래스에다가 from_json, to_json을 setattr함.
