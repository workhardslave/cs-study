## 프로세스 간 통신 (Inter-Process Communication, IPC)
### 정의 
![image](https://postfiles.pstatic.net/MjAyMTA1MjBfMjQz/MDAxNjIxNTEzOTU5NDc2.d7T3zugNKE7WkI90JOvvdCXBvsqSl3x_q7ag4wb0bMkg.9zvlHXQgcXORizew_lC7CKHR22nvilBHjpflUpoTcYMg.PNG.9503090_/522.png?type=w773)

- 프로세스간 통신이란 ? 프로세스간 데이터 송·수신 → __메모리 공유__
- 프로세스간 메모리 영역은 운영체제에 의해 분리되어 관리된다. 따라서 다른 프로세스의 메모리 영역에 접근하는것이 불가하다.
- 이러한 이유로 프로세스간 통신을 위한 도구가 필요하다.

</br>

### 분류

![image](https://postfiles.pstatic.net/MjAyMTA1MjBfMTE2/MDAxNjIxNTE2MDk3MDg1.-u7-zT8dbnld4TtBLq1BKzUkSnXop3YhM-krkdW17cgg.IRLSSrNlZ5efnXZJVQMhAPTr2sDeb-q1HVoUou3exWwg.PNG.9503090_/스크린샷_2019-05-02_오후_4.20.22.png?type=w773)

  - 동기화 도구
  - 데이터 교환
    - 데이터 전송 방식
    - 메모리 공유 방식 

![image](https://postfiles.pstatic.net/MjAyMTA1MjFfMjg5/MDAxNjIxNTI4Njk3NjY5.918OQWV89NdCBt0KOdPONTbqHOpshYN02dmB0l4juPEg.o7_swvua05UNoGlfO12-XoCOG_qv3bu9MjqdoJCHClkg.PNG.9503090_/img-4.png?type=w773)

<br><br/>

## 종류

### 1. Pipe

- 부모 프로세스와 자식 프로세스간에 통신을 할때 사용하는 프로세스 간 통신 기법
- `fork()`를 사용하여 부모, 자식 프로세스를 만들어 각 프로세스를 각 코어에 동시 실행이 가능하게하여 빠른 실행을 하기 위함. 즉, 병렬 처리를 하여 빠른 프로그램의 실행이 목적.
- 반이중 방식 (Half-duplex)으로 한번에 한쪽 방향으로만 통신 가능

  

### 2. Shared Memory

- 프로세스간 메모리 영역을 공유해서 사용할 수 있도록 하여 통신하는 기법. 
- 프로세스가 공유 메모리 할당을 커널에 요청하면 커널은 해당 프로세스에 메모리 공간을 할당. 이후 어떤 프로세스건 해당 메모리영역에 접근할 수 있다.
- 공유 지역이므로 세마포어를 통해 여러 프로세스가 동시에 접근하는것을 막아야 한다.
  

### 3. Message Queue
- 메시지큐는 메시지를 queue 데이타 구조 형태로 관리한다. 
- 메시지큐는 커널에서 전역적으로 관리된다.
  - 모든 프로세스에서 접근가능
  - 메시지큐의 접근자(식별자)를 아는 모든 프로세서는 동일한 메시지큐에 접근하여 데이타를 공유할수 있음.


</br>

## 요약

|종류|PIPE|Shared Memory|Message Queue|
|------|---|---|---|
|용도|부모-자식 간 통신|다른 프로세스와 통신|다른 프로세스와 통신|
|공유 매개체|파일|메모리|메모리|
|통신단위|Stream|구조체|구조체|
|통신방향|단방향|양방향|양방향|


 



<br></br>

## RPC (Remote Procedure Calls)

### 정의 
- IPC는 주로 로컬 (같은 시스템 내 프로세스) 프로세스 커뮤니케이션 
- RPC는 네트워크를 통해 연결된 다른 시스템에 상주하는 프로세스간의 통신, 즉 물리적으로 다른 시스템에 위치하는 프로세스 끼리의 통신이다.
- IDL(Interface Definition Language)를 활용하여 원격에 위치한 프로세스를 로컬에 있는 프로세스처럼 사용할 수 있다.
 
 
### 구조 및 동작  
 
![image](https://postfiles.pstatic.net/MjAyMTA1MjBfNDgg/MDAxNjIxNTE1MDk1OTM2.XvuKdzSxpftIMStpeQ-VXuTXKyBOyKa9HMDGkmbus2Mg.P_Kl03UdmMK4lTG2kbiC6Geddad-mna5h8F_PLbD_mog.PNG.9503090_/99D26A4E5BFF80270D.png?type=w773)


- 1. IDL(Interface Definition Language) 을 사용하여 서버의 호출 규약을 정의한다
  - 함수명, 인자, 반환값에 대한 데이터형이 정의된 IDL 파일을 rpcgen 컴파일러를 이용하여 stub 코드를 자동으로 생성한다.
- 2. Stub는 원시소스코드 (C코드등) 형태로 만들어지므로 클라이언트,서버 프로그램에 포함하여 빌드한다
- 3. 클라이언트 프로그램 입장에서 자신의 프로세스 주소공간의 함수를 호출하는 동일하게 stub 에 정의된 함수를 호출할 수 있게 된다
- 4. stub 코드는 데이터형을 XDR(eXternal Data Representation) 형식으로 변환하여 RPC 호출을 실행한다.
  - XDR 변환 이유는 기본 데이터 타입(정수형, 부동소수점 등)에 대한 메모리 저장방식(리틀엔디안, 빅엔디안)이 CPU 아키텍쳐별로 다르며, 네트워크 전송과정에서 바이트 전송 순서를 보장하기 위함이다.
- 5. 서버는 수신된 함수/프로시저 호출에 대한 처리 완료 후, 결과값을 XDR 변환하여 반환한다.
- 6. 최종적으로 클라이언트 프로그램은 서버의 결과값을 반환받는다.



<br></br>

> Reference  </br>
> [파이프(PIPE) 개념과 예제](https://reakwon.tistory.com/80) </br>
> [IPC(inter process communication) 종류 (Linux)](https://doitnow-man.tistory.com/110) </br>
> [컴퓨터 프로세스간 통신(IPC)](https://velog.io/@hyun0310woo/9.-운영체제-프로세스와-프로세스의-대화) </br>
> https://www.slideshare.net/WonchangSong1/rpc-restsimpleintro </br>
> https://www.slideshare.net/ZiKaS/introduction-to-remote-procedure-call </br>
> https://www.youtube.com/watch?v=biDbn7FBbLs </br>
> https://leejonggun.tistory.com/9 </br>
> https://www.inflearn.com/course/리눅스IPC프로그래밍#reviews </br>
> https://www.joinc.co.kr/w/Site/system_programing/IPC/MessageQueue </br>
