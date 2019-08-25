# SNS 로그인에서 백엔드의 역할 - 로직 구성 편
SNS 로그인은 어케 할까ㅡㅡ 옛날엔 그냥 클라가 대충 ID 주는 거 따로 인증 안 하고 DB에 때려놓곤 했는데 너무 아닌 것 같았다. 몇몇 좋은 백엔드 오픈소스 코드들이 있으니 이걸 읽으면서 알아보자.

## 코드 리딩 대상
- [pyconkr-api](https://github.com/pythonkr/pyconkr-api)
- [velog](https://github.com/velopert/velog)

## 목표
- SNS 로그인에 관련된 전체적인 로직을 어떻게 설계하면 좋을지 알아본다.

## pyconkr-api에서는
핵심 로직은 `api/oauth_tokenbackend.py` 모듈에 있는 듯.

OAuth 프레임워크의 standard 때문인지, 서비스들마다 client id 개념과 access token을 가져올 수 있는 URL이 제공된다. requests_oauthlib을 통해 인증을 처리하는데, flow는 아래처럼 이루어진다.

- client id와 redirect uri을 통해 OAuth2 Session을 얻는다.
- 이 세션(그냥 객체 ㅋㅋ) 가지고 token url, client secret, code를 보내서 토큰을 얻어온다. 세션 객체에 이게 inject되는듯?
- 이제 이걸로 profile 정보를 반환해주는 API를 호출한다.
- 좀 비정상적인 거 있으면 raise하고, 안 그러면 열심히 데이터 받아온다.
- 이거 갖고 UserModel에 데이터 insert한다. 여러 정보를 이어붙여서 username을 만들어 관리하고 있다. `f'{oauth_setting.env_name}_{oauth_type}_{profile_data["id"]}'`같은 식이어서, `develop_github_3424`처럼 만들어짐. 이런 방식이 best way일지는 잘 모르겠다.
- 다 쓰고 나서야 알았는데 이 UserModel이라는 게 별도로 정의한 모델이 아니라 어떤 django extension이 지원해주는 auth 패키지의 일부인가보다.

### 내가 모르는 부분
- 전반적인 OAuth의 흐름 : client id, redirect uri, client secret, code
- django contrib의 auth : 알 필요가 있을까?

### 알아보자
한국 블로그 글은 너무 추상적으로 정리해 둬서 영어권으로 넘어가기 전에 [생활코딩 강의](https://opentutorials.org/course/2473/16571)를 보기로 했다. 일단 이고잉은 설명을 참 잘하는 것 같다. '내가 하는 질문에 답변할 수 없다면 다음에 보는 것이 좋다'라는 흐름은 나도 블로그에 글 쓸 때나 발표에 써먹고 싶다.

- client id, client secret : 여기서 client는 OAuth 서비스를 사용할 주체이기 때문에 그냥 WAS라고 생각해야 한다. google cloud platform같은 데서 받아내는 것. 클라이언트 사이드에서는 OAuth URL로 리다이렉션할 때 쓸 client id 정도만 필요할듯?
- code : resource owner(페이스북으로 로그인하고자 하는, 우리 서비스의 사용자)가 resource server(페이스북)로부터 '얘가 이만큼 쓴다고 하는데 허용할거임?'에 yes하면, 이게 redirect uri 측으로 발급됨. 여기까지 client side에서 처리할 수 있겠다.
- access token : 이제 위에 있는 3개 가지고 access token을 주라고 요청하면 발급받을 수 있음. client secret이 필요한 거라 서버 사이드에서 처리해야 할 것 같다.

SNS 로그인에 써먹을 서비스가 구글이라고 치고, 이걸 흐름에 따라 정리하면,

1. google cloud platform에 들어가서, 세팅하고 client id랑 client secret을 얻어온다.
2. client id랑 redirect uri를 query string에 넣어서, 구글 로그인 창으로 사용자를 넘긴다. 이 사용자가 로그인 하고 이래저래 허용 다 하면 구글이 redirect uri로 코드를 넘겨준다.
3. 이 코드랑 client secret을 갖다가 access token을 얻어온다.
4. 이걸 가지고 사용자 정보를 조회하면 된다!

1~2는 클라이언트에서 처리하면 될 것 같고, 3~4는 서버에서 처리하면 될 것 같은데, 현실은 다를 수 있으니 이제 pyconkr-api을 구체적으로 읽어 보면 되겠다. graphql 써서 읽기 힘듬 ㅡㅡ

1. argument로 auth_type, client_id, code, redirect_uri를 받는다. 클라이언트 사이드에서 자기가 갖고 있는 client id로 열심히 쿵짝쿵짝 해서 code까지 받아다가 API로 쏴주는 듯.
2. 요청으로 받은 client id, redirect uri를 가지고 OAuth2 session을 만든다. google의 경우 scope를 같이 넣기도 함.
3. 이 세션을 가지고 secret이랑 code를 더 넘겨서 access token을 얻어온다.
4. 이제 이거 갖고 이것저것 데이터 가져온다.

전체적으로 내가 예상하던 것과 비슷한데, 의문점은,

- client id랑 redirect uri을 요청에서 왜 받는지 모르겠다. 이게 code 받아올 때랑 다르면 문제가 있어서 sync 맞추려고 이렇게 해주는건가? 매뉴얼에 나오지 않는 거라 잘 모르겠음.
- code를 받으려면 redirect uri를 전달해야 하고, 거기에 resource server가 데이터 쏴주면 code 뽑아서 API로 전달해야 함. 이걸 클라이언트 사이드에서 싹 처리하고 있으려나? 쉽지 않을텐데 SDK가 잘 도와줘서 쉽게 할 수 있는 케이스일까?

일단 pyconkr api 이거 계속 읽어봤자 더 얻을 수 있는 정보가 딱히 없을 것 같으니 벨로그로 넘어가기로!

## velog에서는
Node.js지만 난 할 수 있다. 먼저,

- social auth를 분리한 리소스 구조가 깔끔한 것 같다. `/verify-social/:provider(github|facebook|google)`, `/register/:provider(github|facebook|google)` 같은 형태인데, path parameter로 provider를 분리한 아이디어가 넘 좋음.

이것저것 소셜 로그인과 관련된 리소스들이 많은 것 같은데, verifySocial 함수를 먼저 살펴보기로 했다. 아니 근데 ㅋㅋ 이게머임?

```
type BodySchema = {
    accessToken: string,
};

const { accessToken }: BodySchema = (ctx.request.body: any);
```

왜 요청으로 access token을 받는거냐구요;;; 그럼 프론트에서 client secret까지 다 갖고 있단 말임? 그래서 벨로그에 실제로 들어가서 로그인을 해봤더니 이렇게 요청하고 있음.

```
Request URL: https://api.velog.io/auth/callback/facebook/login?next=/trending
Request Method: GET
```

엥 내가 예상하던 거랑 너무 다른곳에 요청하고 있어서 저쪽 코드를 보니 꽤 간단함. `redirectFacebookLogin` 함수인데, 그냥 facebook oauth 페이지로 리다이렉트 해주는 것밖에 없다. 일단 무조건 리다이렉트 시키고 보는 구조인듯. 여기서 로그인을 하게 되면 설정되어 있는 아래 redirect uri로 데이터가 날아갈 것.

```
https://api.velog.io/auth/callback/facebook
```

이걸 핸들하고 있는 `facebookCallback` 함수를 보자.

1. 코드가 없으면 다시 리다이렉트 시킴!
2. 콜백으로 코드가 왔을테니, 가지고 있던 client id, client secret 가지고 access token을 요청!
3. 이게 잘 있으면, random string을 만들어 hash하고 여기에 access token을 엮어 redis에 expire를 두어 set한다.
4. 그리고 nextUrl을 생성. 기본적으로 `https://velog.io/callback`이고, 이런저런 query string을 붙여주고 있는데 중요한 건 3번 step에서 만든 hash를 `token`이라는 이름으로 넣어주고 있다는 점.
5. 이 nextUrl로 사용자를 리다이렉트!

자 이제 호스트가 `api.velog.io`가 아니기 때문에 프론트 코드를 봐야할 수 있겠다. 페이스북으로 실제로 로그인을 해 보니 아래 URL로 리다이렉트 됐다.

```
https://velog.io/callback?type=facebook&key=5ca5936dafc449534ebea5b41d5d35ee97ca057fcd42735b71ce276d201ef8470172ded26d0d02f9&next=/trending
```

여기서 순차적으로 아래 API call이 진행되었다.

```
Request URL: https://api.velog.io/auth/callback/token?key=5ca5936dafc449534ebea5b41d5d35ee97ca057fcd42735b71ce276d201ef8470172ded26d0d02f9
Request Method: GET
--
Request URL: https://api.velog.io/auth/verify-social/facebook
Request Method: POST
--
Request URL: https://api.velog.io/auth/login/facebook
Request Method: POST
```
차례대로 보자.

### /auth/callback/token(getToken)
query string에 있던 `key`를 뜯어서 query string으로 넘긴다. 서버 입장에선 이 hash의 출처가 redis일테니, redis에 쿼리해서 access token을 얻어와 리턴해줌. 없으면 400.

### /auth/verify-social/facebook(verifySocial)
처음에 봤던 함수를 다시 보게 됨. 클라이언트에서 처리하고 있는 게 아니라, 서버에서 이래저래 잘 해서 전달해 준 것이었음.

- 요청으로 전달된 provider랑 access token 가지고 프로필 정보를 조회하는데, 데이터가 없으면 401을 반환해 회원가입 쪽 플로우를 타게 함.
- 사용자 이메일 정보 가지고 매칭시켜 주기도 하는듯. 예를 들어 google로 로그인했을 때 이메일이랑 facebook으로 로그인했을 때 이메일이랑 같으면 같은 유저로 인식하는 식? 정확히는 모르겠고 스키마 볼 때 확실히 알 수 있겠다.

### /auth/login/facebook(socialLogin)
- 여기도 뭐 access token 가지고 프로필 정보 조회해서 데이터 없으면 회원가입하게 함.
- social id로 유저 찾고, 없으면 다시 이메일로 유저 찾음. 없으면 회원가입이지 뭐ㅋㅋ
- 이메일로 유저가 찾아졌다면 link한다는데 이 부분은 잘 모르겠다. 회원가입이랑 스키마 볼 때 알아봐야 함.
- 이래저래 잘 끝났으면 JWT token이랑 같이 프로필 정보 반환해줌.

### 컨셉 정리
컨셉을 정리해 보자.

- 소셜 로그인을 하면 일단 무조건 리다이렉트 시킨다! provider가 알아서 로그인을 시키던 자동으로 넘기던 해서 redirect uri로 code를 넘겨줄거니까.
- 이 코드로 access token을 생성하고, 이걸 참조하는 별도의 unique hash를 만들어 query string에 붙인 채 어떤 URI로 리다이렉트 시킨다.
- 리다이렉트된 곳에서는 (아마)자바스크립트가 동작해 query string에 있던 hash를 가져다가 access token을 반환해 주는 API를 호출.
- 이거 가지고 막 쿵짝쿵짝 함.

## 내 스타일대로 해 본다면
웹에 한정된 구조라서, 앱에 가능하게 하려면 좀 개조해봐야 함.

- 소셜 로그인을 하면 일단 무조건 리다이렉트 시킨다! 이건 앱에도 적용할 수 있고 꽤 좋은 구조처럼 보인다. 근데 이 리다이렉트를 클라이언트에서 할지 서버에서 할지는 잘 모르겠음. client id를 보호한답시고 서버에서 리다이렉트 시켜줘 봤자 어차피 query string에 들어가는 거라 이러한 보호가 의미가 있을 지는 모르겠다. 차라리 플랫폼마다 redirect uri를 구성하는 형태가 달라서(안드로이드 앱은 URI Scheme, iOS는 Universal Link, ...) 클라이언트에 client id 주고 맡기는 게 나을 것 같다.
- 이렇게 서버의 개입 없이 클라이언트가 code를 얻게 만들었다. 그냥 code만 요청으로 받아도 되겠지만, 코드를 받을 때 쓴 redirect uri와 access token을 받을 때 쓴 redirect uri가 다르면 문제가 발생한다고 했으니 이것도 받자. client id는 받아도 되고 안 받아도 될 듯. 나는 sync 맞춘다 생각하고 받을 것 같다. 그럼 이제 pyconkr-api가 얘네 두 개를 다 받는 이유 해결. 이제 context가 서버로 넘어간다.
- 서버가 받은 것 : client id, code, redirect uri인 상태에서, 서버가 secret key를 가지고 있을테니 이걸로 access token을 얻어올 수 있고, 사용자 정보를 조회할 수 있게 된다. provider가 제공하는 unique id를 가지고 유저 정보를 쿼리하고, 없으면 가입시키고, 대충 로그인시켜 줌!

### 요약
- 소셜 로그인 시 클라가 oauth 처리하고, code랑 거기에 쓴 데이터들 모아다가 서버로 날림
- 서버는 그거 갖다가 access token 만들고 이 사람이 누군가 하고 이런저런 로그인 처리해 줌

일단 이게 가장 간단한 케이스를 커버하는 로직이고, 회원가입에 추가 정보가 필요한 케이스같은 건 더 고민해봐야 할 듯.
