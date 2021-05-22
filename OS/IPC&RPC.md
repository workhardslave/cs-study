# 프로세스 간 통신 (Inter-Process Communication, IPC)
## 1. 정의 
![image](https://postfiles.pstatic.net/MjAyMTA1MjBfMjQz/MDAxNjIxNTEzOTU5NDc2.d7T3zugNKE7WkI90JOvvdCXBvsqSl3x_q7ag4wb0bMkg.9zvlHXQgcXORizew_lC7CKHR22nvilBHjpflUpoTcYMg.PNG.9503090_/522.png?type=w773)

- 프로세스간 통신이란 ? 프로세스간 데이터 송·수신 → __메모리 공유__
- 프로세스간 메모리 영역은 운영체제에 의해 분리되어 관리된다. 따라서 다른 프로세스의 메모리 영역에 접근하는것이 불가하다.
- 이러한 이유로 프로세스간 통신을 위한 도구가 필요하다.

</br>

## 2. 분류

![image](https://postfiles.pstatic.net/MjAyMTA1MjBfMTE2/MDAxNjIxNTE2MDk3MDg1.-u7-zT8dbnld4TtBLq1BKzUkSnXop3YhM-krkdW17cgg.IRLSSrNlZ5efnXZJVQMhAPTr2sDeb-q1HVoUou3exWwg.PNG.9503090_/스크린샷_2019-05-02_오후_4.20.22.png?type=w773)

  - 동기화 도구
  - 데이터 교환
    - 데이터 전송 방식
    - 메모리 공유 방식 

![image](https://postfiles.pstatic.net/MjAyMTA1MjFfMjg5/MDAxNjIxNTI4Njk3NjY5.918OQWV89NdCBt0KOdPONTbqHOpshYN02dmB0l4juPEg.o7_swvua05UNoGlfO12-XoCOG_qv3bu9MjqdoJCHClkg.PNG.9503090_/img-4.png?type=w773)

<br><br/>

## 3. IPC 기법

### 1. Pipe
![img](https://hzqtc.github.io/image/pipe1.png)
- 부모 프로세스와 자식 프로세스간에 통신을 할때 사용하는 프로세스 간 통신 기법
- `fork()`를 사용하여 부모, 자식 프로세스를 만들어 각 프로세스를 각 코어에 동시 실행이 가능하게하여 빠른 실행을 하기 위함. 즉, 병렬 처리를 하여 빠른 프로그램의 실행이 목적.
- 반이중 방식 (Half-duplex)으로 한번에 한쪽 방향으로만 통신 가능

  

### 2. Shared Memory
- 프로세스간 메모리 영역을 공유해서 사용할 수 있도록 하여 통신하는 기법. 
- 프로세스가 공유 메모리 할당을 커널에 요청하면 커널은 해당 프로세스에 메모리 공간을 할당. 
- 공유 메모리의 ID값을 아는 프로세스는 누구나 해당 메모리영역에 접근할 수 있다.
  

### 3. Message Queue

![img](https://postfiles.pstatic.net/MjAyMTA1MjJfMjA1/MDAxNjIxNjc1NzgxMDU5.POccW4M2R--1UGi-i7ijzMihFV9bTksG7YuzKqerUDIg.wcJ1YEkBSf_ZuTMm-GviJxGSEn0N9fbePV4w10Q7kKgg.JPEG.9503090_/1-2.JPG?type=w773)
- 데이터를 주고받기 위한 메세지 큐를 커널에서 관리하는 방식 
  - 모든 프로세스에서 접근가능
  - 메시지큐의 접근자(식별자)를 아는 모든 프로세서는 동일한 메시지큐에 접근하여 데이타를 공유할수 있음.
- 메시지큐는 메시지를 queue 데이타 구조 형태로 관리함 


</br>

## 4. 요약

|종류|PIPE|Shared Memory|Message Queue|
|------|---|---|---|
|용도|부모-자식 간 통신|다른 프로세스와 통신|다른 프로세스와 통신|
|공유 매개체|파일|메모리|메모리|
|통신단위|Stream|구조체|구조체|
|통신방향|단방향|양방향|양방향|


- 커널 ipc perm 구조 (Kernel ipc perm structure)
  - IPC 객체의 허가사항(permission)
  - 생성자(creator)와 소유자(owner) 대한 정보(uid, etc)를 포함

``` C
 struct ipc_perm {
    uid_t   uid;    // 구조체 생성한 사용자 ID
    gid_t   gid;    // 생성한 그룹 ID
    uid_t   cuid;   // 구조체 소유한 사용자 ID
    gid_t   cgid;   // 소유자 그룹 ID
    mode_t  mode;   // 구조체에 대한 접근 권한 (read/write) + (user/group/others)
    uint_t  seq;     
    key_t   key;    
    int     pad[4];  
}
``` 


<br></br>

# RPC (Remote Procedure Calls)

## 1. 정의 
- IPC는 주로 로컬 (같은 시스템 내 프로세스) 프로세스 커뮤니케이션 
- RPC는 네트워크를 통해 연결된 다른 시스템에 상주하는 프로세스간의 통신
- IDL(Interface Definition Language)를 활용하여 원격에 위치한 프로세스를 로컬에 있는 프로세스처럼 사용할 수 있다.
 
</br> 

## 2. 인터페이스 

- 최상위 계층 
  - 사용자들이 직접호출할 수 있는 시스템이 제공하는 RPC 함수들이 존재
  - 일반 메소드를 호출하는 것처럼 사용할 수 있음
- 중간 계층
  -  Stub : 서버와 클라이언트가 서로 다른 주소 공간을 사용 하기 때문에 (메모리 매개 변수에 대한 포인터가 다른 데이터를 가리키지 않도록) 함수 호출에 사용된 매개 변수를 꼭 변환해주는 기능 
     - client stub : 함수 호출에 사용된 파라미터의 변환(Marshalling, 마샬링) 및 함수 실행 후 서버에서 전달 된 결과의 변환
     - server stub : 클라이언트가 전달한 매개 변수의 역변환(Unmarshalling, 언마샬링) 및 함수 실행 결과 변환

</br>  

## 3. 동작  
 
![image](https://postfiles.pstatic.net/MjAyMTA1MjBfNDgg/MDAxNjIxNTE1MDk1OTM2.XvuKdzSxpftIMStpeQ-VXuTXKyBOyKa9HMDGkmbus2Mg.P_Kl03UdmMK4lTG2kbiC6Geddad-mna5h8F_PLbD_mog.PNG.9503090_/99D26A4E5BFF80270D.png?type=w773)

**① IDL(Interface Definition Language) 을 사용하여 호출 규약 정의.**

- 함수명, 인자, 반환값에 대한 데이터형이 정의된 IDL 파일을 rpcgen으로 컴파일하면 stub code가 자동으로 생성된다.
- Stub Code에 명시된 함수는 원시코드의 형태로, 상세 기능은 server에서 구현된다.

**② 생성된 stub 코드를 클라이언트/서버에 함께 빌드**

**④ client에서 stub 에 정의된 함수를 사용시 client stub은 RPC runtime을 통해 함수 호출**
- RPC RunTime : Server와 Client를 Binding하는 Layer

**⑤ server는 수신된 procedure 호출에 대한 처리 후 결과 값을 반환**

**⑥ 최종적으로 Client는 Server의 결과 값을 반환받고, 함수를 Local에 있는 것 처럼 사용할 수 있음.**


## 4. 한계
- 구현의 어려움/지원 기능의 한계 등으로 제대로 활용되지 못함
- Web을 활용한 REST가 RPC를 대체 


</br>

# gRPC (google Remote Procedure Call)

- google 에서 개발한 오픈소스 RPC(Remote Procedure Call) 프레임워크 
- 프로토콜 버퍼 기반 Serizlaizer과 HTTP/2를 결합
- Proto File만 배포하면 환경과 프로그램 언어에 구애받지 않고 프로세스 간의 데이터 통신이 가능


## HTTP/2
- http/1.1 : 클라이언트의 요청이 올때만 서버가 응답을 하는 구조. 매 요청마다 connection을 생성해야 함.
- http/2 : 하나의 connection으로 동시에 여러 개 메시지를 주고 받으며, header를 압축하여 중복 제거 후 전달하여 훨씬 효율적.
![img](https://miro.medium.com/max/542/0*RbY-0HC7iCxiAJCI)

## Protocol Buffer
- 구조화된 데이터를 직렬화(Serialization)하는 기법
  - 직렬화 : 데이터 표현을 바이트 단위로 변환하는 작업
- 예시
  - text 기반 json : 82 byte 
  - protocol buffer : 33 byte
    -  userName같은 불필요한 속성값을 숫자(1,2,3,...)로 대체
    -  처음 1바이트에는 메타 정보, 두 번째 바이트는 뒤로 이어질 데이터의 길이를 명시
    -  데이터 크기가 작아 통신속도가 빠르고, json과 달리 파싱할 필요가 없음
![img](https://miro.medium.com/max/1400/0*EqWBu3VDbav3svJk)


## Proto File
- 프로토콜 버퍼를 사용하기 위한 데이타형
  - 프로토콜 버퍼는 하나의 프로그래밍 언어가 아니라 여러 프로그래밍 언어를 지원
- Proto File : 특정 언어에 종속성이 없는 형태의 데이타 타입. 여러 프로그래밍 언어를 지원하기 위해 도입.
  - protoc 컴파일러로 proto file을 컴파일하면, 각 언어에 맞는 형태의 데이타 클래스 파일을 생성해준다.

ex.
https://meetup.toast.com/posts/261





<br></br>
<details>
<summary> Reference </summary>
<div markdown="1">

> - [파이프(PIPE) 개념과 예제](https://reakwon.tistory.com/80) 
> - [IPC(inter process communication) 종류 (Linux)](https://doitnow-man.tistory.com/110) 
> - [컴퓨터 프로세스간 통신(IPC)](https://velog.io/@hyun0310woo/9.-운영체제-프로세스와-프로세스의-대화)
> - [리눅스 IPC 프로그래밍 - 이론과 실습](https://www.inflearn.com/course/리눅스IPC프로그래밍#reviews)
> - [메세지 큐](https://www.joinc.co.kr/w/Site/system_programing/IPC/MessageQueue)
> - [RPC 개념](https://leejonggun.tistory.com/9)
> - [원격 프로시저 호출](https://ko.wikipedia.org/wiki/원격_프로시저_호출)
> - https://www.slideshare.net/WonchangSong1/rpc-restsimpleintro 
> - https://www.slideshare.net/ZiKaS/introduction-to-remote-procedure-call 
> - [조대협의 블로그](https://bcho.tistory.com/1182)
> - https://meetup.toast.com/posts/261
> - https://medium.com/naver-cloud-platform/nbp-기술-경험-시대의-흐름-grpc-깊게-파고들기-1-39e97cb3460
> - https://blueyikim.tistory.com/m/57
> - https://jeong-pro.tistory.com/m/190
</div>
</details>



