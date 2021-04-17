# Deadlock, Race Condition, 세마포어&뮤텍스
동일 프로세스 내의 스레드들은 부모 프로세스의 메모리를 공유한다. 즉, 하나 이상의 스레드가 동일한 자원에 동시에 접근 할 수 있다. 
이로인해 쓰레드가 같은 자원을 쓰려 할 때 Deadlock, Race Condition 등의 문제점이 발생한다. 이를 방지하고자 동기화 기법인 세마포어와 뮤텍스를 사용하여 스레드 안전(Thread-safety) 을 유지한다.
<br></br>
※ 스레드 안전(Thread-safety) : 어떤 함수나 변수, 혹은 객체가 여러 스레드로부터 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없는 상태<br></br>
<br></br>

## 1. Deadlock
2개 이상의 프로세스가 다른 프로세스의 작업이 끝나기만 기다리며 작업을 더 이상 진행하지 못하는 상태. 즉, 프로세스들이 서로가 가진 자원을 기다리며 Indefinite blocking(starvation) 된 상태.

![img](https://github.com/workhardslave/cs-study/blob/main/OS/img/Deadlock,%20Race%20Condition,%20%E1%84%89%E1%85%A6%E1%84%86%E1%85%A1%E1%84%91%E1%85%A9%E1%84%8B%E1%85%A5&%E1%84%86%E1%85%B2%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3_0.png?raw=true)

### 1. 발생조건 
__네가지 조건을 모두 만족시에 Deadlock이 발생한다.__

  - 1) Mutual exclusion (상호배재) : 한 프로세스가 사용하는 자원은 다른 프로세스와 공유할 수 없는 배타적인 자원이어야 한다. 배타적인 자원은 임계구역으로 보호되기 떄문에 다른 프로세스가 동시에 사용할 수 없다. 따라서 배타적인 자원을 사용하면 교착 상태가 발생한다.</br>
  - 2) No Preemtion (비선점) : 한 프로세스가 사용 중인 자원은 중간에 다른 프로세스가 빼앗을 수 없는 비선점 자원이어야 한다. 어떤 자원을 빼앗을 수 있다면 시간 간격을 두고 자원을 공유할 수 있다. 하지만 자원을 빼앗을 수 없으면 공유할 수도 없으므로 교착 상태가 발생한다.</br>
  - 3) Hold and Wait (점유와 대기) : 프로세스가 어떤 자원을 할당받은 상태에서 다른 자원을 기다리는 상태여야 한다. 다른 프로세스의 작업 진행을 방해하는 교착상태가 발생하려면 다른 프로세스가 필요로 하는 자원을 점유하고 있으면서 또 다른 자원을 기다리는 상태가 되어야 한다.</br>
  - 4) Circular wait (원형 대기) : 점유와 대기를 하는 프로세스 간의 관계가 원을 이루어야 한다. 프로세스가 특정 자원에 대해 점유와 대기를 한다고 해서 모두 교착상태에 빠지는 것은 아니다. 점유와 대기를 하는 프로세스들이 서로 방해하는 방향이 원을 이루면 프로세스들이 서로 양보하지 않기 때문에 교착 상태에 빠진다.</br>

### 2. 해결방안 
  
  - 1) Prevention : 위 네가지 조건 중 하나라도 만족시키지 못하면 Deadlock이 발생하지 않는다. 따라서 발생조건을 원천적 차단하여 Deadlock 발생을 방지한다. </br>
  - 2) Avoidance : Deadlock의 가능성 전혀 없을때만 자원을 할당하여 Deadlock 발생 방지. 
  - 3) Recovery : Deadlock이 발생한 이후 회복하는 방식. 프로세스를 모두 한번에 죽이거나, 하나씩 죽이는 방법 존재.
  - 4) Ignorance : 데드락에 대해 아무일도 하지 않음 __Wndow 포함 대부분의 OS가 채택__ → 운영체제가 관여 안하면 사용자가 알아서 프로세스를 죽임. Deadlock은 빈번히 발생하는 이벤트가 아니기 때문에 많은 오버헤드가 드는 방식(1~3)이 비효율적임.

<br></br>
## 2. Race Condition
두 개 이상의 프로세스가 공유 자원에 동시 접근하여 __데이터 접근 순서에 따라 실행 결과가 달라지는__ 상황. 
![img](https://github.com/workhardslave/cs-study/blob/main/OS/img/Deadlock,%20Race%20Condition,%20%E1%84%89%E1%85%A6%E1%84%86%E1%85%A1%E1%84%91%E1%85%A9%E1%84%8B%E1%85%A5&%E1%84%86%E1%85%B2%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3_1.png?raw=true)
<br></br>
## 3. 동기화 기법 

공유데이터 접근영역 (Critical section, 이하 임계영역)에 동시접근 문제를 소프트웨어적으로 해결하기 위한 조건은 다음과 같다.

  - 1) Mutual exclusion : 상호배재. 한 프로세스가 임계영역에 있으면 다른 프로세스가 접근하지 못함

  - 2) Progress : 임계영역이 비어있으면 들어갈 수 있어야 한다.

  - 3) Bounded Wating : 기다리는 시간이 유한해야 한다 → starvation 방지 <br></br>
  

### (1) example 1
  - 임계영역에 들어갈 프로세스를 변수 turn을 통해 결정. (상호 교대)
  - Mutual exclusion 만족 
  - 그러나 프로세스 간 임계영역 접근 빈도수가 다른 경우 progress 만족 못함
  ```{.cpp}{
    do{ 
        while(turn ! =0);
        critical section
        turn = 1;
        remainder section
        } while(1);
  ```

### (2) example 2
  - 앞선 변수 turn과 프로세스가 임계영역에 들어갈 의사가 있음을 알려주는 배열 flag 사용. 
  - Mutual exclusion 만족
  - 그러나 flag를 true로 설정한 직후 프로세스의 CPU 제어권이 넘어가면, 임계영역에 아무 프로세스도 접근하지 못한 상태로 멈추게 됨.
  ```{.cpp}{
    do{ 
        flag[i] = true
        while(flag[j]);
        critical section
        flag[i]=false
        remainder section
        } while(1);
  ```

### (3) example 3 (Peterson's Algorithm)
  - flag와 turn을 모두 사용하여 임계영역에 접근.  
  - 동기화 조건을 모두 만족시킨다.
  ```{.cpp}{
    do{ 
        flag[i] = true
        turn =j
        while(flag[i]&&turn==j);
        critical section
        flag[i]=false
        remainder section
        } while(1);
  ```
<br></br>

## 4. Semaphore & Mutex
세마포어는 위처럼 임계영역 접근시 발생할 수 있는 소프트웨어적 에러를 간소화 시킨 추상 자료형이다. 프로그래머 입장에서는 위와같은 동기화 작업을 실제 구현하지 않고 P(S), V(S)와 같은 연산을 통해 임계영역에 접근할 수 있다. 

※ 추상 자료형 : 논리적 자료형. 컴퓨터에서 구현된것이 아닌, 특정 작업을 나열해 둔 자료형. 시스템에 따라 구현방법은 달라지지만 목적은 같다. 

### (1) 특성
- integer variable : 세마포어는 변수는 정수값이다.
- atomic operation : 세마포어는 두개의 atomic 함수로 조작된다. 
  - 임계구역 접근 P(S)
  - 임계구역 탈출 V(S)
### (2) 종류
- Binary sempahore __(=Mutex)__
  - 세마포어는 변수가 1인 경우 (자원의 개수가 하나인 경우)
  - Mutual exclusion (Lock/unlock) 용도로 사용함.
- Counting semaphore 
  - 세마포어 변수가 1이상인 경우 (자원의 갯수가 여분이 있으면 여러 프로세스가 접근할 수 있음)
  - 여분의 자원을 counting 하는 용도

### (3) Example : Readers-Writers Problem
- 데이터를 읽는 프로세서(Readers)와 데이터를 쓰는 프로세서(Writers) 존재
- 공유 데이터
  - int readcount = 0; (현재 데이터에 접근 상태인 Reader의 수)
  - DB 자체
- 동기화 변수
  - semaphore mutex =1; (공유변수 readcount를 접근하는 코드(Critical section)의 Mutual exclusion 보장 위한 Mutex(Locking 역할))
  - semaphore db=1; (자원(DB)에 접근할 수 있는 프로세스를 1로 제한하는 Semaphore)
### [Writer] 
데이터를 변경하는 작업은 다수의 프로세스가 동시에 수행할 수 없음. 따라서 세마포어(db)를 통해 데이터 변경 중 다른 프로세스가 공유 DB에 접근하는것을 막음. 
```{.cpp}{
     P(db) 
     ...
     writing DB is performed
     ...
     V(db)
```

### [Reader]
읽는 행위는 동기화에 영향을 미치지 않음 -> 다수의 Reader가 읽는 것 가능. 단, 읽는 도중 데이터가 수정되는것을 방지하기 위해서는 Writers 프로세스의 접근을 방지해야 한다. </br>
따라서, 첫번째 Reader가 임계영역 DB에 접근하기 위한 세마포어를 획득 P(db)) 해야한다. </br>
다만 이를 확인하기 위한 변수 readcount 역시 공유데이터이기 때문에 이에 접근하는 부분은 critical section이며, 이를 위해서는 mutex를 통해 Lock하는 과정 필요.
```{.cpp}{
     P(mutex)
     readcount++
     if(readcount==1) P(db) 
     V(mutex)
     ...
     reading DB is performed
     ...
     P(mutex)
     readcount--;
     if(readcount==0) V(db)
     V(mutex)
```

Readers-Writers Problem 는 세마포어를 통해 프로세스 간 동기화를 가능하게 하지만, Reader가 계속해서 들어오는 경우에는 Writer가 Starvation 상태가 될 수 있다.
<br></br>

### (4). 세마포어(Semaphore) vs 뮤텍스(Mutex) 
- 뮤텍스는 Locking 메커니즘으로 __락을 걸은 쓰레드만이__ 임계 영역을 나갈때 락을 해제할 수 있다. 
- 세마포어는 Signaling 메커니즘으로 락을 걸지 않은 쓰레드도 signal을 사용해 락을 해제할 수 있다. 
- 세마포어 변수를 1로 설정하면 뮤텍스처럼 활용할 수 있다. *세마포어와 뮤텍스를 별개로 보거나, 뮤텍스를 세마포어의 일종으로 보는 것으로 나뉘는것 같아요* 

<br></br>
## 5. Monitor
운영체제 커널로 제공되는 세마포어와 뮤텍스와 달리 모니터는 프레임워크나 라이브러리 그 자체에서 제공되는 동기화 기법이다. 자바 쓰레드 동기화 모델은 __모니터__ 개념을 적용하고 있다. 
  
- 시스템 호출과 같은 개념. 인터페이스만 제공한다
- 임계구역으로 지정된 변수나 자원에 접근하고자 하는 프로세스는 직접 P()나 V()를 사용하지 않고 모니터에 작업 요청을 한다. 
- 모니터는 요청받은 작업을 Wait Queue에 저장한후 순서대로 처리하고 그 결과만 해당 프로세스에 알려준다.
- 세마포어보다 간단한 방법으로 임계구역을 보호할 수 있다.
- example code : https://parkcheolu.tistory.com/15


<br></br>


>Reference</br>
>http://www.kocw.net/home/cview.do?cid=5c3c30382c7bbcf6 </br>
>http://www.kocw.net/home/search/kemView.do?kemId=1046323 </br>
>https://junyongs.wordpress.com/type/image/ </br>
>https://m.blog.naver.com/PostList.nhn?blogId=skwd123 </br>
>https://parksb.github.io/article/10.html </br>
>https://mangkyu.tistory.com/104 </br>
>https://seungahyoo.tistory.com/69 </br>

>Image Resource</br>
>https://prepinsta.com/operating-systems/deadlock-introduction/</br>
>https://gsis.kaist.ac.kr/board/view?bbs_id=research&bbs_sn=1152&menu=183</br>
