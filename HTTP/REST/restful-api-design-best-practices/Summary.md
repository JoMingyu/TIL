# 배운 것들
## 이건 잘 안 지키고 있었는데 바로 다음번 API부터 써먹어볼 수 있겠다
- URL 디자인 시 collection url, single resource url로 나누기
- URL에 복수 명사 사용
- PUT은 full update, PATCH는 partial update 용도로 잘 사용하기
- GET/PUT/PATCH/DELETE에 대해 idempotent를 잘 지키기
- POST에서 성공 시, `Location` 헤더로 생성된 자원의 정보를 전달하기
- 데이터를 `data` 필드에 감싸기
- 404를 남용하지 않기
- 에러 메시지를 조금 더 유용하고 자세하게 제공하기
- relationship 개념 이용하기
- continuation token을 사용한 pagination 제공하기

## 아 이거 좀 애매하다
- URL에 명사 사용하기. 회원가입과 로그인은 어떻게 URL을 디자인해야 할 지 애매하다. URL의 사용자 관점이 아니라 리소스 관점에서 디자인한다고 치면, `POST /users`, `POST /users/1/tokens`같은 식이 될 수 있을 것 같은데, 좀 어렵다.
- Query String으로 complex parameter를 받기. 기존에도 필터를 query string으로 지원하긴 했는데, `filter[id]=1,2`같은 형태는 너무 생소하다. 파싱하기도 좀 애매할 것 같은데, 널리 알려진 모습이 아니라서 인지도 높은 라이브러리가 있을 것 같지도 않음.
- HATEOAS 잘 지키기. 뭐 pagination이 들어간 API에서 `links` 하위에 `next`같은 프로퍼티로 '다음 collection을 위한 링크'를 제공하는 정도까지야 할 수 있을 것 같은데, 조금 더 복잡한 케이스는 어렵다. 그동안 해왔던 API 개발 패턴과 아예 패러다임이 다른 느낌이라 좀 더 공부해야봐야 할 듯.
