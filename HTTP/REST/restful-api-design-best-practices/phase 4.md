## Ensure Evolvability of the API
### Avoid Breaking Changes
모든 API는 stable해야 한다. 따라서 breaking change는 지양해야 함. 클라이언트에 crash를 나지 않게 하는 선에서 어째 API를 발전시켜 나갈 수 있을까?

- 이전 버전과 호환되어야 함. 필드를 추가하는 것은 문제가 없음.
- duplication -> deprecation 형태가 되어야 함. 기존 필드를 변경하려면, 레거시 필드 옆에 새 필드를 추가하고 문서에 레거시 필드의 deprecation을 명시하자.
- hypermedia와 HATEOAS를 열심히 가꿔놓자. 클라이언트가 API-driven하게 움직이면 변경이 용이하다고 함. 예를 들어 게시글 목록에서 '다음 페이지'를 요청하는 경우, API response에 명시된 링크를 타는 식이 되는 게 확장성이 더 높음.

### Keep Business Logic on the Server-Side
서비스가 low level의 API만을 제공해서 단지 data access layer만으로만 사용되면 high coupling을 만들 수 있다고 함. 비즈니스 요구사항에 따른 데이터의 워크플로우가 클라이언트/서버 간에 분산되면, 이 둘 간의 결합도가 높아짐. 따라서 data access 형태의 API 작성을 피해야 함. 새로운 비즈니스 요구 사항이 생기면, 클라이언트와 서버를 모두 변경하는 breaking change가 생기게 될 수 있음.

따라서 high level의 API를 구축해야 함.

## Consider API Versioning
위의 접근법이 먹히지 않아서, 다른 버전의 API를 제공해야 하는 상황이 발생할 수 있음. 버전 관리를 사용하면 클라이언트 사이드의 여러 요구 사항들을 신경쓰지 않고 새로운 릴리즈를 할 수 있으며, 클라이언트는 자신들의 속도대로 천천히 마이그레이션해도 된다는 장점은 있으나, 이런저런 논쟁이 있다고 함. 여러 버전의 API를 오랫동안 maintaining해야 한다는 점이 문제. versioning은 두 가지 방법으로 제공할 수 있는데, URL을 사용하는 방식과 header를 사용하는 것이 있음.