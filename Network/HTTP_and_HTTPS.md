# HTTP와 HTTPS(HTTP Secure)

## 1. HTTP (Hypertext transfer protocol)

HTTP는 인터넷에서 사용하는 웹 서버와 사용자의 인터넷 브라우저 사이에 문서를 전송하기 위한 통신 규약이다.

- OSI 7계층 중 응용 계층에 위치한 프로토콜 
- 포트 넘버 80
- 전송 내용이 보호되지 않기 때문에 해커의 공격 및 스니핑에 매우 취약
- 비상태연결 (Stateless, Connectless) 
  - 요청(Request)과 응답(Response)으로 구성
  - 서버에 연결하고 요청해서 응답을 받으면 연결을 끊는다
  - 장점 : 접속유지 최소화, 불특정 다수를 대상으로 하는 서비스에 유리
  - 단점 : 연결을 끊어버리기 때문에 클라이언트의 이전 상태를 알 수 없음. (쿠키 사용하여 보완)

## 2. HTTPS (Hypertext Transfer Protocol Secure) 
- HTTP를 SSL/TLS를 통해 암호화하여 컴퓨터 네트워크를 통한 통신을 보안하도록 설계된 웹 통신 프로토콜
  - 기존 HTTP는 TCP와 직접 통신하지만 HTTPS는 HTTP는 SSL/TLS와 통신함.
- 제3자 인증, 공개키 암호화, 비밀키 암호화를 사용하여 보안
- 포트 넘버 443
- https://tech.ssut.me/https-is-faster-than-http/

<br></br>

# SSL (Secure Sockets Layer) 

## 1. 정의

- SSL은 인터넷 상에서 데이터를 안전하게 전송하기 위한 인터넷 암호화 통신 프로토콜이다.
- 응용계층과 전송계층 사이에서 독립적으로 동작한다.

![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/HTTP_and_HTTPS2.png?raw=true)

</br>

## 2. 암호화

 - SSL은 보안과 성능상의 이유로 두가지 암호화 기법을 혼용해서 사용한다. 
 - 키(key) : 암호를 만드는 행위인 암호화를 할 때 사용하는 일종의 비밀번호  

### 1) 대칭키

![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/HTTP_and_HTTPS3.gif?raw=true)

- 암호화와 복화에 같은 암호 키(대칭키)를 사용하는 기법 
- 암호화 및 복호화가 빠름 
- 대칭키를 공유해야 하기 때문에, 대칭키가 유출되는 해킹의 위험에 노출 될 수 있음.



### 2) 공개키

![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/HTTP_and_HTTPS4.gif?raw=true)



- 암호화와 복호화에 사용하는 암호키를 분리 
  - 비밀키 (Private Key) : 자신이 가지고 있는 고유한 암호키
  - 공개키 (Public Key) : 대중에 공개되는 키. 비밀키로만 복호화할 수 있음.

- 사용 시나리오 : 
  1. A가 웹 상에 공개된 'B의 공개키'를 이용하여 평문을 암호화 한다. 이 암호문은 B가 개인적으로 가지고 있는 B의 비밀키로만 복호화가 가능하다. 
  2. B는 자신의 비밀키로 복호화한 평문을 확인하고, A의 공개키로 응답을 암호화하여 A에게 보낸다. 
  3. A는 자신의 비밀키로 암호화된 응답문을 복호화한다. 


</br>

### 3) 대칭키와 공개키를 이용한 SSL의 암호화 기법

SSL에서는 대칭키 방식을 사용하고 키를 전달하는 방식은 공개키 방식을 사용한다. 다음과 같은 절차로 수행된다.

1. A는 B에게 공개키를 전달

2. B는 A에게 전달받은 공개키를 이용해서 자신의 대칭키를 암호화. 
</br>B가 암호화한 대칭키는 오직 A가 가지고 있는 개인키로만 복호화가 가능하기 때문에 해킹의 위험이 없음
4. A도 위와 같은 절차를 반복
5. A와 B는 서로의 대칭키를 보유하고 있기 때문에 서로의 대칭키로 암호화하여 전달.
   - 서로의 대칭키로 암호화를 하기 때문에 비용 감소 (공개키 단점 보완)
   - 공개키 방식을 사용했기 때문에 키 전달의 보안 위험 해결 (대칭키 단점 보완)
   - 클라이언트는 서버가 신뢰할 수 있는 서버인지 확인 가능


<br></br>
__실제 사용 시나리오는 다음과 같다.__

1. 사이트는 사이트 정보와 사이트의 공개키를 인증기관에 전달
2. 인증기관은 사이트 정보와 사이트의 공개키는 인증기관의 개인키로 암호화한 후, 브라우저에 전달
3. 클라이언트는 사이트의 접속을 할 때, 인증기관이 개인키로 암호화한 사이트 정보와 사이트 공개키 수신
4. 클라이언트는 인증기관으로부터 받은 인증기관 공개키를 이용해서 사이트가 전달한 것을 복호화 한다. 사이트가 전달한 것은 인증기관이 개인키로 암호화했기 때문에 클라이언트는 인증기관의 공개키로 복호화 가능하다.


> - __SSL 인증서__ : 클라이언트와 서버간의 통신을 제 3자가 보증하는 전자화된 문서. 클라이언트가 서버에 접속을 하면, 서버는 클라이언트에게 인증서를 전달하고, 클라이언트는 인증서가 신뢰할 수 있는 정보인지 검증 후 사이트의 정보와 서버의 공개키를 얻을 수 있음.

<br></br>
## 3. 통신과정

HTTPS는 상대방의 공개키를 획득하여 암호화해서 상대방에게 암화된 정보를 제공하고 신원을 확인하는 절차인 SSL Handshake을 통해 동작한다. 다음과 같은 절차로 수행된다.

#### 1단계. 클라이언트 요청 (**Client Hello**)

- 클라이언트의 TCP 3 way handshake 이후 연결을 요청하는 Client Hello 전송

#### 2단계. 서버 응답 (Server Hello)

- 서버가 클라이언트에게 인증서를 전달

#### 3단계. 클라이언트의 인증서 신뢰 확인 

- 클라이언트가 받은 인증서를 공개키로 검증
- 이 인증서는 인증기관의 개인키로 암호화되어 있고, 공개키로 검증할 수 있다. (브라우저에 내장) 

  - 클라이언트는 사이트의 정보와 서버의 공개키를 얻음

- 데이터를 주고 받을 때 사용할 비밀키를 서버로 전송 (공개키 방식을 이용)

#### 4단계. 서버측에서 클라이언트가 보낸 비밀키 값을 복호화

- 비밀키를 확인하여 이후 통신을 공유된 비밀키로 암화하여 통신 


<br></br>

## 4. TLS (Transport Layer Security) 

SSL(Secure Socket Layer)이라는 명칭이 표준화되면서 바뀐 이름

- SSL 1.0 > SSL 2.0 > SSL 3.0 > TLS 1.0 

 - TLS 1.0은 SSL 3.0 버전을 계승하여 만들어졌지만 상호 운용되지 않음 (현재 SSL은 보안상 취약해서 거의 사용되지 않기 때문)

<br></br>

# HSTS (HTTP Strict Transport Security)

## 1. 정의

웹사이트 접속시 HTTP 요청을 HTTPS 으로 강제하는 방법. 즉, HTTPS를 지원하는 웹 사이트에서 자신은 HTTPS만 사용해서 통신할 수 있음을 접속하고자 하는 웹 브라우저에게 알려 주는 기능.



## 2. 리다이렉트 응답을 통한 HTTPS 강제 방법의 한계

웹 브라우저가 HTTP Protocol 로 특정 도메인에 접속을 시도하는 경우, 해당 도메인이 HTTPS 프로토콜만을 지원하는 웹사이트라면 ”301 Redirect” 또는 ”302 Redirect”  응답을 보내 웹 브라우저가 HTTPS로 다시 접속하라고 redirect 하여 HTTPS를 통해 웹사이트와 통신한다.

그러나 이 경우 해커와 같은 공격자가 중간자공격(MITM attack)을 하여 중간에 Proxy Server를 두고 사용자와는 HTTP 통신을 하고 실제 사이트와는 HTTPS 통신을 하는 문제가 발생할 수 있다. 즉, 사용자가 실제 사이트와 주고 받는 모든 정보는 공격자에게 노출될 수 있다. 이러한 SSL Stripping attack를 방지하기 위하여 HSTS를 사용해야 한다.



## 3. HSTS 동작

HSTS를 지원하는 웹 브라우저는 내부에 HSTS List를 보유한다. 즉 HTTPS Protocol을 사용해야만 하는 웹사이트에 대한 정보를 가지고 있다. 다음과 같이 동작한다.

1. 클라이언트가 HTTP로 접속을 시도하는 경우, 먼저 브라우저에서 HSTS 설정된 도메인지 확인한다. (HTTP 요청 보내지 않음)

2. HSTS된 사이트에 대해 브라우저는 HTTPS로 서버에 요청한다. (중간자는 SSL로 암호화된 패킷을 도청할 수 없다.)

3. 서버가 HTTPS 요청에 대한 응답을 보낸다.
   - 웹서버는 HTTPS Reply Message에 HSTS Policy를 넣어서 보낸다. (암호화 되기 전 packet인 HTTP response header field에 "Strict-Transport-Security" 필드 정보 삽입) 

4. 서버로 부터 받은 응답을 통해 HSTS List를 구성한다.



따라서 HSTS를 사용하면 사용자가 입력한 도메인이 HSTS List에 있는 지 먼저 확인하여 HTTPS를 사용하여 사이트에 접속할 수 있다. 만약 웹 브라우저가 HTTP Protocol로 웹 서버에 접속하고 HTTP 응답 메세지에 HSTS Policy 정보가 들어 있는 경우는 무시된다.

</br>

> #### HSTS List 
>
> - HSTS는 HTTPS로 웹 사이트에 접속하기 위해 적어도 한번 웹 서버와 통신을 해야하는데, 통신 전에 미리 HTTPS로 접속하도록 목록을 미리 만들어 놓은 것.
>
> - 브라우저에서 지원하는 기능이며, 구글 크롬이 주도하는 [HSTS Preload List](https://hstspreload.org/)가 주로 사용된다. </br>
>   	- 크롬 뿐만 아니라 엣지, 사파리, 파이어폭스 등 다양한 브라우저도 이를 지원하며 동일한 DB를 사용한다. </br>
>   	- 해당 리스트에 사이트가 등록되면 이후 업데이트되는 브라우저는 등록된 도메인을 무조건적으로 HTTPS로 연결하며, HTTPS가 지원되지 않는 경우 연결 자체가 불가하게 된다.


</br>


## 4. 설정방법 

HSTS는 웹서버에서 응답해주는 헤더에 최초 포함되어 있는 만큼 웹서버에서 설정해 주어야 한다. 설정 방법은 HTTP 응답 헤더에  `Strict-Transport-Security`라는 필드를 내려주면 된다. 해당 응답을 받은 브라우저는 그 사이트에 접속할 때 무조건 HTTPS로만 연결한다.

- HSTS response header 요소
  - max-age=31536000 : HSTS가 브라우저에 설정될 시간. 초단위로, 63072000은 2년을 의미한다.
  - includeSubdomains : HSTS가 적용될 도메인의 서브 도메인에도 HSTS를 적용한다.
  - preload : 브라우저가 해당 사이트를 HSTS 적용 preload list에 추가하도록 한다.

```xml
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```



## 5. 특징
- HSTS를 사용하기 위해서는 웹 서버와 브라우저 모두 기능을 지원해야 한다. 
- 표준화된 절차(IETF) 이기 때문에 다양한 웹 브라우저와 디바이스에 호환된다. 
  - Microsoft Edge, 인터넷 익스플로러, 크롬, 파이어폭스, 사파리, 오페라, 그 외 모바일 웹 브라우저 등





<br></br>

### <Reference>

> #### 1. HTTP and HTTPS
>
> 1) https://lkhlkh23.tistory.com/126</br>
> 2) https://velog.io/@hygoogi/%EA%B8%B0%EC%88%A0%EB%A9%B4%EC%A0%91-%EC%A4%80%EB%B9%84%ED%95%98%EA%B8%B0</br>
> 3) https://krksap.tistory.com/1148</br>
> 4) https://sjh836.tistory.com/81</br>
> 5) https://velog.io/@ljh95/ </br> 

> #### 2. SSL and TLS
>
> 1) https://preamtree.tistory.com/38</br>
> 2) https://lkhlkh23.tistory.com/126</br>
> 3) https://blue-shadow.tistory.com/114</br>

> #### 3. HSTS
>
> 1) http://wiki.gurubee.net/display/SWDEV/HSTS+%28HTTP+Strict+Transport+Security%29
> 2) https://luckydavekim.github.io/development/back-end/spring-security/what-is-the-hsts</br>
> 3) https://m.blog.naver.com/PostView.nhn?blogId=aepkoreanet&logNo=221575708943&proxyReferer=https:%2F%2Fwww.google.com%2F
> 4) https://rsec.kr/?p=315

