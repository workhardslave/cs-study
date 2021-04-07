# TCP (Transmission Control Protocol)
## 특성 
TCP는 양종단 호스트 내 프로세스 상호 간에 신뢰적인 연결지향성 서비스를 제공하는 프로토콜로 다음과 같은 특성을 갖는다.
- Connection-Oriented : 연결지향적. 송신과 수신 지점을 연결한 후 통신한다.
- Point to Point : 각 연결이 정확히 2개의 종단점을 가지고 있다.
- In-order delivery : 송신자가 보낸 순서대로 수신자가 데이터를 받는다.</br></br>
ㅤ
![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/TCP%203-way-handshake_0.png?raw=true)
ㅤ

즉, TCP 통신을 위해서는 호스트간의 connection이 요구된다. 이에 앞서, 호스트간 연결을 위해서는 서버에서 socket 생성 → bind → listen → accept 의 과정이 선행되어야 한다.
- bind() : 소켓에 포트 번호 부여 ex) web server port 80
- listen() : 클라이언트 접속 대기
- accept() : 클라이언트의 연결 수락 </br></br>

accept() 이후 서버는 클라이언트에서 연결 요청이 올 때까지 blocking 상태를 유지한다. 클라이언트에서 연결 요청이 들어오면 3-way handshake 를 통해 호스트간 Connection 을 생성한다.</br></br>
ㅤ
## TCP Header
![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/TCP%203-way-handshake_1.png?raw=true)

TCP Connection에서 사용되는 TCP Header 내 파라미터는 다음과 같다.</br></br>
##### (1) SYN Flag 
- 두 호스트를 연결하기 위해 3-way handshake에서 사용되는 플래그
- 송신자와 수신자의 **첫 번째 패킷만** 이 플래그를 설정해야 한다.
##### (2) ACK Flag 
- 호스트에서 패킷을 성공적으로 수신하였다는걸 알리기 위해 사용되는 플래그 
- TCP 연결 후 모든 세그먼트에서 해당값은 1로 셋팅되는데, 이는 Acknowledgement number 필드에 유효한 값이 적혀있다는 의미를 갖는다.
##### (3) FIN Flag 
- 연결 종료를 위해 사용하는 플래그
##### (4) Sequence number
- TCP에서는 데이터를 보낼때 마다 각 데이터에 고유한 번호를 부여해서 전송한다. (In-order 특성) 이 고유한 번호가 바로 Sequence Number이다.
- 이를 통해 수신측에서는 중복된 자료는 폐기하고, 순서가 바뀌어서 수신되는 경우 이를 순서대로 재구성 할 수 있다.
##### (5) Acknowledgement number  
- 수신자가 데이터를 정확히 수신했음을 송신자에게 알려줄때 사용하는 값. 
- 다른 의미로는 다음에 받을 Seq. No를 의미한다. </br></br></br>

# TCP connection (3-way handshake)
## 1) Process
![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/TCP%203-way-handshake_2.png?raw=true)
![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/TCP%203-way-handshake_3.png?raw=true)

TCP는 세번의 메세지를 교환하여 Connection을 생성한다. 이를 3-way handshake라 한다. 다음과 같은 순서로 수행된다.

##### (1) SYN Message
- TCP connection을 연결하기 위해 처음으로 보내는 메세지 → 컨트롤 메세지로 헤더만 존재함.
- SYN flag=1
- Initial Sequence number 포함 

##### (2) SYN-ACK Message
- SYN Message를 수신 후 응답하는 메세지 → 컨트롤 메세지로 헤더만 존재함.
- SYN flag=1, ACK flag =1
- ACK number= 클라이언트의 Initial Sequence nubmer + 1
- 자신의 Initial Sequence number 같이 전송

##### (3) ACK Message
- SYN-ACK Message를 수신 후 응답하는 메세지. → ACK 메세지로 **데이터 포함할 수 있음**
- ACK flag=1
- ACK nubmer = 서버의 Initial Sequence nubmer +1</br></br></br>


## 2) Connection termination
TCP는 connection을 만들고 데이터를 주고받은 뒤 호스트는 connection을 끊어야 한다. 이 때, 통신은 양방향 독립적으로 끊을 수 있다. (full duplex) 즉, 클라이언트가 종료를 요청하더라도, 서버가 데이터 모두 전송한 후 연결 끊을수 있다. 종료 방법은 FIN bit=1 설정한 FIN control segment 를 보내 **4-way handshake** 하는 것이다. 다음과 같은 절차로 수행된다.

![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/TCP%203-way-handshake_5.png?raw=true)

#####  (1) FIN Message (From Client to Server)
- 클라이언트에서 FIN flag=1 인 FIN Control segment를 서버로 전송
- Sequence number 포함.

##### (2) ACK Message
- FIN Message에 대한 응답으로 ACK Message 전송
- ACK flag =1
- ACK number = 클라이언트의 Sequence number + 1

### ===== Client -> Server Connection termination =====
FIN segment와 ACK 주고받으면 클라이언트→ 서버 방향으로의 데이터 커넥션만 끊어짐. 즉, 아직까지 서버는 데이터를 보낼 수 있음. (클라이언트는 수신만 가능)

##### (3) FIN Message (From Server to Client)
- 서버에서 FIN flag=1 인 FIN Control segment를 클라이언트로 전송
- Sequence number 포함.

##### (4) ACK Message
- FIN Message에 대한 응답으로 ACK Message 전송
- ACK flag =1
- ACK number = 서버의 Sequence nubmer + 1

### ===== Server -> Client Connection termination =====

※ 예시에서 클라이언트가 ACK을 보내더라도 얼마간 time wait 를 유지한 후 커넥션이 끊어진다. 만약 client가 마지막으로 보낸 ACK가 손실되면 서버는 timeout 되어 FIN segment를 재전송 할 것이기 때문. (클라이언트는 wait상태에서 ACK 재전송할 수 있음.)</br></br></br>

## 2) half open connection
![img](https://github.com/workhardslave/cs-study/blob/main/Network/img/TCP%203-way-handshake_4.png?raw=true)
- **half open connection : 2-way handshake에 발생하는 문제점**

서버의 ACK Message(3-way handshake의 세번째 메세지) 전송이 늦어져 클라이언트가 connection request를 다시 보낼 수 있다. 만약 이 요청이 늦어져 서버에서 connection termination 이후 도착하게 되면 클라이언트는 모든 통신을 종료한 후지만 서버는 클라이언트와 다시 connection이 있는것으로 생각할 수 있다. 즉, 서버는 연결되지만 클라이언트는 없는 상태가 되는 문제가 존재한다. 

※ 우측은 딜레이된 request와 보내진 데이터 패킷을 새로운 데이터로 인식해 중복된 데이터를 받은 경우</br></br></br></br>



> Reference
> - [https://ppt-online.org/536681](https://ppt-online.org/536681)
> - [https://charsyam.wordpress.com/2018/01/09/입-개발-ipv4-tcp-socket-listen-에서-accept-까지/](https://charsyam.wordpress.com/2018/01/09/%EC%9E%85-%EA%B0%9C%EB%B0%9C-ipv4-tcp-socket-listen-%EC%97%90%EC%84%9C-accept-%EA%B9%8C%EC%A7%80/)
> - [https://medium.com/javarevisited/fundamentals-of-socket-programming-in-java-bc9acc30eaf4](https://medium.com/javarevisited/fundamentals-of-socket-programming-in-java-bc9acc30eaf4)
> - [https://d2.naver.com/helloworld/47667](https://d2.naver.com/helloworld/47667)
> - [http://www.kocw.net/home/search/kemView.do?kemId=1169634&ar=relateCourse](http://www.kocw.net/home/search/kemView.do?kemId=1169634&ar=relateCourse)
> - [http://okminseok.blogspot.com/2019/07/tcp-control-flag.html](http://okminseok.blogspot.com/2019/07/tcp-control-flag.html)
> - [http://www.deadfire.net/tcpip/tcpip20.html](http://www.deadfire.net/tcpip/tcpip20.html)
