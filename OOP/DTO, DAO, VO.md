# [OOP] DTO, DAO, VO

### DTO

> 데이터 전송 객체(Data Transfer Object) 는 프로세스 간에 데이터를 전달하는 객체이다.

- 프로세스 간 통신이 원격 인터페이스(웹 서비스 등)로 이루어지게 되는데, 호출의 비용이 많다는 점을 동기로 하여 이용하게 된다.
- 호출 비용이 커서, 호출 수를 줄이기 위해 데이터를 하나의 호출만으로 서비스 되는 객체인 DTO 이용

위에 말들은 너무 어렵고 이해가 잘 안가서 코드로 다시 살펴보았다

```java
public class User{
    @Column(nullable = false)
    private int userId;
    private String name;
    
    @Builder
    public User(int userId, String name){
        this.userId = userId;
        this.name = name;
    }
    public int getId(){
        return userId;
    }
    public int getName(){
        return name;
    }
}
```

이렇게 객체로 유저를 만들어서 데이터베이스에 있는 내용을 객체로 만든다는 것을 DTO라고 볼 수 있다. 여러 블로그들에서 setter도 DTO에 넣는 것을 볼 수 있었는데, 그렇게 짜는 것 보다, 따로 Controller Layer에서 Response DTO 형태로 전달하는 것이 좋을 것 같다.

참조 - [https://velog.io/@p4rksh/Spring-Boot%EC%97%90%EC%84%9C-%EA%B9%94%EB%81%94%ED%95%98%EA%B2%8C-DTO-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0](https://velog.io/@p4rksh/Spring-Boot에서-깔끔하게-DTO-관리하기)

------

### VO

> VO(Value Object) 는 값 객체이다.

- equals()와 hashcode()를 오버라이딩 한다.
- 속성 모든 값들이 VO 객체마다 값이 같아야 똑같은 객체라고 판별한다.

VO도 Getter 와 Setter를 가질 수 있으며, 값 끼리 비교를 할 때, 값 안에 있는 모든 Attribute를 비교한다.

```java
...
    public boolean equals(Object o){
    if(this == o) return true;
    if(o == null || ...)return false
...
```

------

### DAO

> 데이터 접근 객체(Data Access Object)는 일부 데이터베이스나 기타 Persistence 매커니즘에 추상 인터페이스를 제공하는 객체이다.

- Application의 호출을 Persistence 계층에 매핑시킴으로써 DB의 상세한 사항을 노출시키지 않고, 특정 데이터의 일부 동작을 제공하게 된다.
- 단일 책임 원칙(Single Responsibility Principle)을 지원

웹서버는 DB와 연결하기 위해서 계속 Connection 객체를 생성하는데, 이렇게 된다면 DB에 연결하기 위해 많은 자원이 사용될 수밖에 없다. 그래서 Connection Pool 이라는 것을 사용한다. 먼저 Connection을 만들어 놓고, 필요할 때마다 가져다 쓰는 것이다.

하지만 유저 한 명이 접속해서 하나의 커넥션만 만드는게 아니라, 글쓰기, 읽기 마다 각각 만드는 것도 엄청나게 많은 커넥션이 일어난다.

따라서 Database에 접속하는 객체를 한 개만 만들고, 모든 페이지에서 이 객체로 일을 처리한다는 것이 바로 **DAO** 이다. 자바 코드로 한 번 살펴보자.

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class DAO {

    public void add(User user) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost/test", "root", "root");

        PreparedStatement preparedStatement = connection.prepareStatement("insert into users(id,name) value(?,?)");

        preparedStatement.setInt(1, user.getUserId());
        preparedStatement.setString(2, user.getName());
        preparedStatement.executeUpdate();
        preparedStatement.close();
        
        connection.close();

    }
}
```

------

### 참조

1. [https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%A0%84%EC%86%A1_%EA%B0%9D%EC%B2%B4](https://ko.wikipedia.org/wiki/데이터_전송_객체)
2. [https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%A0%91%EA%B7%BC_%EA%B0%9D%EC%B2%B4](https://ko.wikipedia.org/wiki/데이터_접근_객체)
3. [https://jungwoon.github.io/common%20sense/2017/11/16/DAO-VO-DTO/](https://jungwoon.github.io/common sense/2017/11/16/DAO-VO-DTO/)