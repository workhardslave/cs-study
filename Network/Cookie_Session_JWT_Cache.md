# 쿠키, 세션, JWT, 캐시

HTTP 프로토콜에선 비연결성(Connectionless)과 비상태성(Stateless)이라는 특성이 있다.

**비연결성(Connectionless)**: 클라이언트와 서버가 연결을 맺은 후, 클라이언트 요청에 서버가 응답을 마치면 연결을 끊는 성질

**비상태성(Stateless)**: 비연결성으로 인해 서버가 클라이언트를 식별할 수 없는 성질

이런 특성을 보완하기 위해 쿠키와 세션을 사용.

로그인, 접속시간, 장바구니 등 다양한 사용자 정보를 관리하여 웹사이트에서 사용자 경험을 더욱 편하게 만들어 준다.

## 쿠키

쿠키는 웹 사이트에 접속할 때 서버에서 생성하여 사용자 브라우저에 정보를 담는 임시 파일이다.

쿠키의 데이터 형태는 Key와 Value로 구성되고 String 형태로 이루어져 있다. 크기는 4KB를 넘을 수 없다.

### 사용 목적

**세션 관리(Session Management)**

로그인, 사용자 닉네임, 접속 시간, 장바구니 등 서버가 알아야할 정보를 저장

**개인화(Personalization)**

사용자마다 다르게 그 사람에 적절한 페이지를 보여줄 수 있음

**트래킹(Tracking)**

사용자의 행동과 패턴을 분석하고 기록

### 쿠키 사용 예시

- 아이디 저장, 로그인 상태 유지
- 일주일간 다시 보지 않기
- 최근 검색한 상품을 광고에서 추천
- 쇼핑몰 장바구니 기능

### 동작 과정

1. 클라이언트가 서버에 요청
2. 서버는 HTTP 응답 헤더에 set-cookie 속성을 추가하여 응답 → 클라이언트는 쿠키 저장
3. 클라이언트는 이후 서버에 요청할 때 전달받은 쿠키를 자동으로 요청헤더에 추가하여 요청(브라우저가 자동으로 추가)
4. 서버에서 쿠키를 참고하여 로직 수행

### Session 쿠키와 Permanent 쿠키

- Session 쿠키: 웹 브라우저가 종료될 때 제거되는 쿠키
- Permanent 쿠키: 브라우저가 종료돼도 유지되는 쿠키(Expires 또는 Max-Age 옵션으로 쿠키가 얼마나 유지될지 정해줌)

### Secure, HttpOnly 옵션

- Secure: HTTPS 프로토콜에서만 쿠키를 전송(쿠키 탈취문제 예방)
- HttpOnly: 브라우저에서 쿠키 접근 불가(Cross Site Scripting 공격 방지)

### Cookie vs. LocalStorage

LocalStorage 또한 쿠키와 같이 클라이언트에 정보를 저장하는 방식

- 유효 기간이 없고 영구적으로 이용 가능
- 5MB까지 사용 가능
- 필요할 때 언제든 사용 가능(쿠키는 서버 접속시에 자동 송신)

그러나 LocalStorage는 XSS(Cross Site Scripting)공격에 취약, 그러나 CSRF 공격엔 더 안전

### 쿠키의 단점

- 쿠키에 대한 매 정보를 헤더가 추가해서 보내기 때문에 추가적인 트래픽 발생
- 민감한 정보를 쿠키에 보관하다가 유출되면 문제 발생

## 세션

쿠키의 단점을 보완. 정보를 사용자 브라우저(클라이언트)에 저장하지 않고 서버 단에 저장.

고유한 Session ID를 생성 후 각 클라이언트에 매칭하여 Session ID를 전송해준다. 클라이언트는 해당 Session ID만 헤더에 쿠키로 포함하여 전송하고 서버는 Session ID를 통해 클라이언트를 식별한다.

Session ID는 Hash Table에 저장한다.

### 동작 과정

1. 클라이언트가 서버에 처음으로 요청을 보냄(처음이라 session id가 존재하지 않음)
2. 서버에서 session id를 발급하여 응답
3. 클라이언트가 전달 받은 session id를 매 요청마다 헤더 쿠키에 넣어 요청
4. 서버는 session id를 통해 사용자를 식별
5. 클라이언트가 로그인을 성공하면 서버는 새로운 session id(로그인을 인증받은)를 발급하여 응답
6. 클라이언트는 이 후 요청시 새로 발급 받은(로그인을 인증 받은) session id와 함께 요청. 서버도 이를 통해 로그인 사용자 식별 가능.
7. 클라이언트 종료 시 session id 제거, 서버에서도 제거

### 세션의 단점

- 서버에 데이터를 저장하기 때문에 양이 많아질수록 부하가 커지고 저장 공간 부족 문제 발생
- session id 또한 탈취되면 보안 상 문제 발생

## JWT(JSON Web Token)

JWT란 JSON 형식 웹 토큰으로서 일반적으로 클라이언트와 서버 사이에서 사용자 인증과 권한을 위해 사용된다. C, Python, Java, JavaScript 등 대부분의 프로그래밍 언어에서 지원되어 범용성이 좋고 보안에도 뛰어나 정보를 안정성 있게 전달할 수 있다.

또한 세션처럼 서버에서 별도의 저장소를 필요로 하지 않고 그때 그때 토큰만 검증만 해주면 되니 매우 효율적이다.

### 토큰의 구성

토큰은 <font color="red">Header</font>, <font color="purple">Payload</font>, <font color="blue">Signature</font>로 구성된다.

#### Header(헤더)

토큰의 타입과 해싱할 알고리즘 방식을 담는다. 일종의 metadata 정보를 담고 있다고 보면된다.

#### Payload(정보)

토큰에 담을 정보가 들어간다. 사용자 식별을 위해서 사용자의 고유 ID와 같은 정보가 담긴다.

#### Signature(서명)

<font color="red">Header</font>와 <font color="purple">Payload</font>를 인코딩 후 시크릿과 함께 해싱하여 <font color="blue">Signature</font>를 생성한다.

![JWT](./img/Cookie_Session_JWT_Cache0.png)

위와 같은 방법으로 Header.Payload.Signature 와 같은 형태의 토큰이 생성된다.

> 예) <font color="red">eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9</font>.<font color="purple">eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ</font>.<font color="blue">SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c</font>

이처럼 복잡해 보이지만 해당 토큰을 서버에서 받으면 알고리즘에 따라 해석하고 검증을 하게된다.

### 토큰 전달과 저장 방법

#### 로그인 시

1. 브라우저에서 사용자가 로그인하면 서버에 이메일과 비밀번호를 전송한다.
2. 서버에서 이메일과 비밀번호를 확인 후 사용자가 확인되면 JWT를 생성한다.
3. 브라우저에 생성한 토큰을 보내준다.
4. 브라우저에서 토큰을 받아 쿠키에 저장한다.

이 후 저장된 토큰을 통해 인증된 사용자만 접근할 수 있는 페이지에 접근할 수 있다.

#### 이 후 페이지 접근 시

1. 브라우저에서 저장된 토큰과 함께 서버에 요청을 보낸다.
2. 서버에서 해당 토큰의 Signature를 Secret Key로 복호화한 후, 조작 여부, 유효 기간을 확인한다.
3. 해당 토큰 검증이 완료되면 Payload를 디코딩하고 해석한 정보에 따라 응답

### Refresh Token

JWT의 단점을 보완. JWT(Access Token)의 경우 보안을 위해 유효 기간을 짧게 세팅한다. 그러나 이러면 사용자가 새 토큰을 발급받기 위해 로그인을 자주 시도해야 한다.

이런 문제를 해결하기 위해 Refresh Token을 사용한다. Access Token의 유효 기간이 만료되었을 때 새로 토큰을 발급해주는 역할을 한다.

![RefreshToken](./img/Cookie_Session_JWT_Cache1.png)

## 캐시

- 자주 사용되는 이미지나 html, css, js 파일 등을 브라우저에 저장해놓고 사용하는 것
- 같은 자원을 사용할 때 일일이 불러오지 않고 캐시되어 있는 자원을 사용하여 클라이언트 속도 향상
- 캐시에 있는 것을 재사용하기 때문에 경우에 따라 변경된 자원을 참조할 수 없는 경우가 생김

> Reference
> - [https://cjh5414.github.io/cookie-and-session/](https://cjh5414.github.io/cookie-and-session/)
> - [https://devuna.tistory.com/23](https://devuna.tistory.com/23)
> - [https://server-engineer.tistory.com/152](https://server-engineer.tistory.com/152)
> - [https://nesoy.github.io/articles/2017-03/Session-Cookie](https://nesoy.github.io/articles/2017-03/Session-Cookie)
> - [https://tools.ietf.org/html/rfc6749#section-1.5](https://tools.ietf.org/html/rfc6749#section-1.5)
> - [https://brownbears.tistory.com/44](https://brownbears.tistory.com/440)
> - [https://ponyozzang.tistory.com/341](https://ponyozzang.tistory.com/341)
> - https://www.youtube.com/watch?v=LZq0G8WUaII&ab_channel=TheNetNinja
