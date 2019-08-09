## Use Two URLs per Resource
리소스당 두 개의 URL을 만드는데, `collection url`과 `single resource url` 개념으로 분리하라는 것이다.

### example
```
# URL that represents a collection of resources
/employees          
# URL that represents a single resource
/employees/56    
```

## Use Consistently Plural Nouns
URL에는 `복수 명사`를 사용하라는 내용이다.

### Yes
```
/employees
/employees/21
```

### No
```
/employee
/employee/21
```

`matter of taste`지만 복수형이 더 일반적이라고 한다. 그동안 그냥 단수가 더 깔끔해 보여서 단수를 썼는데, 이제 복수를 써야겠다. 가장 중요한 것은 복수 명사와 단수 명사를 혼합하지 말아야 한다는 것.

## Use Nouns instead of Verbs for Resources
URL 이름에는 동사보단 명사를 사용하라는 내용이다.

### Don't do this
```
/getAllEmployees
/getAllExternalEmployees
/createEmployee
/updateEmployee
```

작은 URL set 내에서, HTTP method로 구분하는 게 더 낫다.

## HTTP Methods
### Use HTTP Methods to Operate on your Resources
HTTP method를 적재적소에 잘 사용하라는 내용. URL은 리소스를 명시하는 데에, method는 리소스에 대한 `what-to-do`를 명시하는 데에 사용하라고 한다.

- Read: Use GET
- Create: Use POST or PUT
- Update: Use PUT and PATCH
- Delete: Use DELETE

나는 PUT method를 `upsert` 용도로만 사용하곤 했는데, 이게 맞는지 의문이 들었다. 바로 아래 섹션에서 이야기함.

### Understand the Semantics of the HTTP Methods
#### GET
- Idempotent
- collection url과 single resource url에 대해서 모두 사용함
- 리소스에 대한 변경이 있으면 안 됨(사이드 이펙트가 없어야 함)
- 응답을 안전하게 캐시할 수 있는 구조여야 함

#### PUT
- Idempotent
- 일반적으로 single resource url에 대해서만 사용함
- create와 update 용도로 사용할 수 있음(example: PUT /employees/1 - 1번 employee가 있으면 업데이트하고, 없으면 생성)
- PUT을 create 용도로 사용하는 경우, 클라이언트는 ID를 포함한 전체 URL을 알아야 함. 그러나 일반적으로 ID를 생성하는 주체는 서버기 때문에 어울리는 형태가 따로 있다. 예로 `PUT /post/13`은 일반적이지 않고, `PUT /employees/1/avatar`는 employee마다 하나의 avatar만 있는 구조라면 설득력 있음.
- PUT이나 PATCH나 update인데, PUT은 `full update` 개념을, PATCH는 `partial update` 개념을 권고한다는 차이가 있다. full upate는 아예 리소스를 덮어쓰기하는 식이고, partial update는 변경하고자 하는 부분만 업데이트하는 식. 예로 `PUT /employees/1/avatar`는 1번 employee의 아바타 정보를 요청의 것으로 설정하되 없으면 생성하라는 `complete replacement` 형태라, 생성을 위한 데이터를 빼먹지 않아야 해서 항상 full payload를 전달해야 함. `PATCH /employees/1/avatar`는 1번 employee의 아바타 정보 중 원하는 몇 가지만 변경하는 형태. 이 내용이 좀 애매하게 느껴지는데 더 읽어보면 이해할 수 있겠다.

#### POST
- Not Idempotent
- 일반적으로 collection url에 대해서만 사용함
- 생성된 리소스에 대한 URL을 `Location` 헤더로 전달해 줘야 한다!(not idempotent인 이유기도 함)

#### PATCH
- Idempotent
- 일반적으로 single resource url에 대해서만 사용함
- partial update를 위해 사용됨

#### DELETE
- Idempotent
- 일반적으로 single resource url에 대해서만 사용함
- Delete를 위해 사용됨

### POST on the Resource Collection URL to Create a New Resource
POST할 때, client-server interaction이 어떤 식인지 알려줌!

- client: 적절한 payload와 함께 POST 요청
- server: 201 Created와 함께 `Location` 헤더로 해당 리소스의 URL을 반환

### PUT on the Single Resource URL for Updating a Resource
PUT은 딱히 특별한 것 없음

- client: `/employees/21`처럼 specified url에 대해 payload와 함께 요청. 여기서는 아예 덮어쓰는 느낌으로 payload를 보냄. 예로 21번 employee의 `status`만을 변경하기 위해 `name`과 `status`를 다 보내는 식.
- server: 별 문제 없다면 200 OK 반환

### Use PATCH for Partial Updates of a Resource
PUT은 partial update에 어울리지 않다는 것을 한 번 더 강조함.
> PUT should only be used for complete replacements of a resource 

PUT의 개념을 따르면, 수정하고 싶을 때마다 모든 데이터를 전달해야 하므로 성능 문제도 있고 병렬로 업데이트하는 경우 원치 않게 데이터를 덮어쓸 수도 있음. 예를 들어 20번 employee의 직급을 수정하고 싶은데 PUT을 쓰면 다른 것들까지 다 보내야 하는 상황. 따라서 PATCH는 변경하고 싶은 것만 보낸다. 이게 `full update`와 `partial update`의 차이. 그리고 validation 등 이런저런 이유로 이 사람은 그냥 편하게 PATCH 쓰는 걸 추천하고 있음.

- client: `/employees/21`처럼 specified url에 대해 payload와 함께 요청. PUT과 다르게, PATCH는 변경하고 싶은 데이터만 전달한다. `status`만 변경하고 싶으면 정말로 `status`만 보냄.
- server: 별 문제 없다면 200 OK 반환
