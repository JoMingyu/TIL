## Design Relationships Appropriately
`employee`는 자신에게 할당된 `manager`가 있고, 본인의 팀에 속한 `teamMembers`가 있는 형태의 relationship을 API에서 어떻게 표현할지에 대한 내용.

### Links
#### Example
```
{
  "data": [
    { 
      "id": 1, 
      "name": "Larry",
      "relationships": {
        "manager": "http://www.domain.com/employees/1/manager",
        "teamMembers": [ 
          "http://www.domain.com/employees/12",
          "http://www.domain.com/employees/13"
        ]
        //or "teamMembers": "http://www.domain.com/employees/1/teamMembers"
      }
    }
  ]
}
```
- payload size가 적음. 클라이언트가 항상 manager와 teamMembers가 필요한 게 아니라면 필요할 때만 호출하게 최소한의 정보만 제공하는 형태임.
- 클라이언트가 relationship의 정보를 자주 로드한다면 request가 많아질 수 있음.
- 데이터를 client side에서 잘 조합해 써야 함.

### Sideloading
#### Example
```
{
  "data": [
    { 
      "id": 1, 
      "name": "Larry",
      "relationships": {
        "manager":  5 , 
        "teamMembers": [ 12, 13 ]
      }
    }
  ],
  "included": {
    "manager": {
      "id": 5, 
      "name": "Kevin"
    },
    "teamMembers": [
      { "id": 12, "name": "Albert" }
      , { "id": 13, "name": "Tom" }
    ]
  }
}
```

`Compound Documents`라고도 부름. `relationships`에는 FK만 두고, `included`에 페이로드를 더 적는 식.

- request count를 줄일 수 있음.
- 링크로 주는 것보다 payload size가 커질 수 있음.
- relationship을 쓰기 좋게 정제하기 위해 클라이언트가 번거로울 수 있다는데 공감은 잘 안 됨. 링크로 주는 거랑 별 차이 없다는 입장이라면 동의. 이렇든 저렇든 재조합은 해야 하니까.

### Embedding
#### Example
```
{
  "data": [
    { 
      "id": 1, 
      "name": "Larry",
      "manager": {
        "id": 5, 
        "name": "Kevin"
      },
      "teamMembers": [
        { "id": 12, "name": "Albert" }
        , { "id": 13, "name": "Tom" }
      ]
    }
  ]
}
```

- 클라이언트에게 가장 편리하다고 함. 임베딩되어 있으니 이 전 것들보다는 편하기야 할 것 같음.
- 클라이언트가 필요로 하지 않더라도 relationship을 주는 식이라 불필요한 payload size 증가가 일어날 수 있음. 이건 `GET /employees?include=manager,teamMembers` 같은 식으로 query string을 받으면 어느 정도 해결할 수 있을 듯.

## Use CamelCase for Attribute Names
일반적으로 다들 아는 내용. RESTful 서비스는 JavaScript로 작성된 클라이언트가 사용하는 케이스가 드물다는 것과, JavaScript Object Notation이기 때문에 카멜 케이스를 써야 한다.

## Use Verbs for Operations
`Operation`을 명시할 땐 URL에 동사를 써도 된다고 함. `RPC-style API`라고 부름. 리소스와 연관되지 않은 것들이 기준.

### Example
```
//Reading
GET /translate?from=de_DE&to=en_US&text=Hallo
GET /calculate?para2=23&para2=432

//Trigger an operation that changes the server-side state
POST /restartServer
//no body

POST /banUserFromChannel
{ "user": "123", "channel": "serious-chat-channel" }
```

## Provide Pagination
데이터베이스의 모든 리소스를 한 번에 리턴하는 건 좋지 않다. 그러니 pagination을 써야 한다고 함. `Offset-based pagination`과 `Keyset-based pagination`이 널리 사용되는 두 가지 방법이라고 함. 후자를 추천한다고 한다.

### Offset-based Pagination
query string에 `offset`, `limit`, `skip`, `size`같은 필드를 집어넣게 해서 이걸 기반으로 pagination을 지원하는 방법. 안 보내면 기본값으로 채우는 등의 로직이 들어가곤 한다.

#### Example
```
/employees?offset=30&limit=15 # returns the employees 30 to 45
/employees                    # returns the employees 0 to 100
```

### Keyset-based Pagination (aka Continuation Token, Cursor)
`Offset-based Pagination`은 구현하긴 어렵지 않은데 단점이 많다고 한다.

- 큰 테이블에선 `OFFSET` 쿼리가 느리다고 함.
- pagination을 하는 동안 엘리먼트에 변화가 생기는 것과 같은 문제가 생길 수 있음

Index가 걸려있는 컬럼 기준으로 pagination할 수 있으면 속도도 괜찮고 좋을텐데, 그 방법이 바로 `keyset-based pagination`이라고 함. 데이터의 생성 시점이 타임스탬핑된 컬럼이 있어야 편히 구현할 수 있을듯? 아래는 이에 따라 created time을 기준으로 pagination하는 요청의 예.

```
GET /employees?pageSize=100                
# The client receives the oldest 100 employees sorted by `data_created`
# The last employee of the page has the `data_created` field  with 1504224000000 (= Sep 1, 2017 12:00:00 AM)

GET /employees?pageSize=100&createdSince=1504224000000
# The client receives the next 100 employees since 1504224000000. 
# The last employee of the page was created on 1506816000000. And so on.
```

`createdSince`라는 파라미터를 통해 pagination하는 식. offset-based에서 문제가 되었던 여러 점들을 해결할 수 있겠지만 더 개선할 여지가 있다.

- id와 같은 추가 정보를 created time에 추가해서 continuation token을 만들면 신뢰성과 효율성을 향상시킬 수 있다. 이건 [글이 따로](https://phauer.com/2018/web-api-pagination-timestamp-id-continuation-token/) 있어서 읽어봐야 할 듯.
- 클라이언트가 response에 있는 다른 엘리먼트를 살펴보고 pagination 관련 값을 뽑아내게, 그냥 링크를 주는 게 클라이언트도 편하고 HATEOAS 관점에서도 낫다.

이런 관점에서, `GET /employees?pageSize=100`의 response는 아래와 같이 만들어질 수 있다.

```
{
  "pagination": {
    "continuationToken": "1504224000000_10",
  },
  "data": [
    // ...
    // last element:
    { "id": 10, "dateCreated": 1504224000000 }
  ],
  "links": {
    "next": "http://www.domain.com/employees?pageSize=100&continue=1504224000000_10"
  }
}
```

## Check out JSON:API
[JSON:API](https://jsonapi.org/) 좀 살펴보라는 내용. 작성자는 여기 쓰여진 권장 사항들이 과도하게 느껴져서, 이걸 기반으로 상황 따라 적합하게 써먹는다고 함.
