## Wrap the Actual Data in a data Field
실제 데이터를 JSON object의 `data` field에 감싸라고 한다. [JSON:API Standard](https://jsonapi.org/)에 맞고, 메타데이터를 추가하기 위한 공간을 남겨두기 유용하다고 함.

## Use the Query String (?) for Optional and Complex Parameters
### No
```
GET /employees
GET /externalEmployees
GET /internalEmployees
GET /internalAndSeniorEmployees
```
### Yes
```
GET /employees?state=internal&title=senior
GET /employees?id=1,2
```
### JSON:API
JSON:API Standard에서는 이런 식으로 한다고 함

```
GET /employees?filter[state]=internal&filter[title]=senior
GET /employees?filter[id]=1,2
```

## Use HTTP Status Codes
`status code 어울리게 잘 써라`는 당연한 내용인데, 404를 과도하게 사용하지 말라는 권고가 있다. 리소스가 사용 가능하지만 사용자가 볼 수없는 경우 403 Forbidden을. 리소스가 한 번 존재했지만 지금 삭제 또는 비활성화 된 경우 410 Gone을 사용하는 등으로 더 정확하게 표현하라고 함.

## Provide Useful Error Messages
Response에서 에러 메시지를 조금 더 유용하고 자세하게 제공하라고 함.
### Example
```
{
  "errors": [
    {
      "status": 400,
      "detail": "Invalid state. Valid values are 'internal' or 'external'",
      "code": 352,
      "links": {
        "about": "http://www.domain.com/rest/errorcode/352"
      }
    }
  ]
}
```
이것도 JSON:API Standard에 기반한 이야기.

## Provide Links for Navigating through your API (HATEOAS)
리소스들은 하이퍼링크로 연결되어야 한다. REST의 필요조건.

### Example
`GET /employees`로 요청했을 때, response payload는 아래처럼 되어야 한다고 함.

```
{
  "data": [
    {
      "id":1,
      "name":"Paul",
      "links": [
        {
          "salary": "http://www.domain.com/employees/1/salaryStatements"
        }
      ]
    }
  ]
}
```

GitHub API가 HATEOAS를 정말 잘 지켰던 것 같음.
