# [Pynamodb](https://github.com/pynamodb/PynamoDB)
boto같은 걸로도 dynamodb를 컨트롤할 수 있지만, Pythonic interface를 지원하는 라이브러리가 있다.

## 모델 정의
여타 ORM 쓰듯이 모델을 정의할 수 있다.

```python
from pynamodb.models import Model
from pynamodb.attributes import UnicodeAttribute

class Thread(Model):
    class Meta:
        table_name = 'Thread'
    forum_name = UnicodeAttribute(hash_key=True)
    thread_name = UnicodeAttribute(null=True, attr_name='tn')
```
`hash_key`가 dynamodb의 partition key, `range_key`가 dynamodb의 sort key라고 보면 된다. `Meta`라는 이름의 inner class로 이런저런 메타 정보들을 명시하는데, region이나 host같은 것도 쓸 수 있어서 로컬에 dynamodb를 띄웠을 때 쉽게 호스트를 변경할 수 있게 된다.

## 테이블 crud
```python
if Thread.exists():
    Thread.create_table(read_capacity_units=1, write_capacity_units=1)
    Thread.describe_table()
    Thread.delete_table()
```

create_table할 때 capacity unit 정보를 전달해야 한다. 또는 아예 Meta에 명시해 두거나.

## 데이터 crud
MongoEngine이랑 비슷하게 생겨먹었다.

```python
Thread('forum_name', 'forum_subject', replies=10).save()
thread_item = Thread.get('forum_name', 'forum_subject')
thread_item.thread_name.set('new forum subject!')
thread_item.delete()

query_result = Thread.query('forum_name', Thread.thread_name.contains('subject'), limit=10)
for thread in query_result:
    ...

_ = query_result.last_evaluated_key
```

모델에 전달한 positional argument 두 개는 차례대로 partition key, sort key 자리에 알아서 들어가고, 나머지 필드들은 keyword argument로 전달해 추가하면 된다. `query` 메소드가 이런저런 기능들이 많은데, 시그니처를 보면 이해하기 편하다.

```python
def query(
    cls,
    hash_key,
    range_key_condition=None,
    filter_condition=None,
    consistent_read=False,
    index_name=None,
    scan_index_forward=None,
    limit=None,
    last_evaluated_key=None,
    attributes_to_get=None,
    page_size=None,
    rate_limit=None
):
    ...
```

`scan_index_forward`로 sort key 기준 오름차순/내림차순 정렬을 컨트롤할 수 있고, 쿼리 결과에서 `last_evaluated_key`라는 필드로 dynamodb가 반환하는 last evaluated key를 가져다가 다시 인자에 전달해 pagination도 할 수 있다. hash key가 required인 건 dynamodb query 자체가 partition key가 필수이기 때문.

scan도 할 수 있고, [batch read/write](https://pynamodb.readthedocs.io/en/latest/batch.html)도 가능. 좋은 라이브러리!
