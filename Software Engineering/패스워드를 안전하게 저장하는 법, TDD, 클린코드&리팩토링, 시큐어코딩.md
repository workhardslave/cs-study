# 1. 패스워드를 안전하게 저장하는 법
## 1) 패드워드 저장 방식
- 단방향 해시 함수(one-way hash function)의 다이제스트(digest)

비밀번호를 데이터베이스에 저장하는 대신 다이제스트를 저장하는 것이 보편화되고 있다. 국내에서는 '개인정보보호법'과 '정보통신망 이용촉진 및 정보보호 등에 관한 법률'을 통해 비밀번호, 주민등록번호와 같은 개인정보를 반드시 암호화해야 한다고 규정되어 있다.

## 2) 해시 함수
![image](https://user-images.githubusercontent.com/49704910/118095414-023b1b00-b40b-11eb-90fe-4f479ddf812c.png)
- 해시 함수(Hash Function) : 임의의 길이의 메시지를 입력으로 받아 고정된 길이의 해시 값을 출력하는 함수
- 암호화 해시 함수의 특징은 **'단방향'** 이라는 것이다. 비밀번호를 알면 다이제스트를 바로 연산할 수는 있지만, 다이제스트만으로는 비밀번호를 역추적하는 것이 거의 불가능하다.
- 해시 함수에는 암호 키가 사용되지 않기 때문에, 같은 입력에 대해서 항상 같은 해시 값을 얻을 수 있다. 
- 해시 함수는 입력 값의 일부가 변경되었을 때 다이제스트가 완전히 달라지도록 설계되어 있다. (avalanche 효과)


## 3) 해시 함수의 문제점
### (1) 인식 가능성(recognizability)
- 동일한 메시지가 언제나 동일한 다이제스트를 갖기 때문에 발생하는 문제
- 공격자가 전처리(pre-computing)된 다이제스트를 가능한 한 많이 확보한 다음 이를 탈취한 다이제스트와 비교해 원본 메시지를 찾아내거나 동일한 효과의 메시지를 찾을 수 있다. 
	- 이와 같은 다이제스트 목록을 레인보우 테이블(rainbow table)이라 하고, 이와 같은 공격 방식을 레인보우 공격(rainbow attack)이라 한다. 
- 다른 사용자의 패스워드가 같으면 다이제스트도 같으므로 한꺼번에 모두 정보가 탈취될 수 있다.
- 솔팅을 통해 개선 가능 

### (2) 속도(speed)
- 해시 함수는 원래 짧은 시간에 데이터를 검색하기 위해 설계된 것으로 빠른 처리 속도로 인해 공격자는 매우 빠른 속도로 임의의 문자열의 다이제스트와 해킹할 대상의 다이제스트를 비교할 수 있다
- 이런 방식으로 패스워드를 추측하면 패스워드가 충분히 길거나 복잡하지 않은 경우에는 그리 긴 시간이 걸리지 않는다. 
  
  
 ## 4) 해시 함수 보완
 ### (1) 솔팅 (salting)
- 솔트 (salt) : 단방향 해시 함수에서 다이제스트를 생성할 때 추가되는 바이트 단위의 임의의 문자열
- 솔팅 (salting) : 원본 메시지에 문자열을 추가하여 다이제스트를 생성하는 것
- 동일한 비밀번호에 대해 항상 동일한 다이제스트가 생성하는 해시 함수의 문제점을 보완해주는 것 
- 솔트와 패스워드의 다이제스트를 데이터베이스에 저장하고, 사용자가 로그인할 때 입력한 패스워드를 해시하여 일치 여부를 확인할 수 있다. 
  - 이 방법을 사용할 때에는 모든 패스워드가 고유의 솔트를 갖고 솔트의 길이는 32바이트 이상이어야 솔트와 다이제스트를 추측하기 어렵다.
  ![img](https://files.itworld.co.kr/archive/image/2015/06/hash_function02(1).png)
  
 
 ### (2) 스트레칭(Streching)
- 스트레칭 (Streching) : 암호화 해시 함수를 수천 번에서 혹은 수만 번을 반복하는 것. 
- 무작위 공격으로 비밀번호를 추측하는 데 많은 시간이 소요되도록 하는 방법으로, 반복 횟수를 늘리는 것만으로도 방어력을 효과적으로 높일 수 있다는 장점이 있다.

 ![img](https://files.itworld.co.kr/archive/image/2015/06/hash_function03.png)
 
 ### (3) Adaptive Key Derivation Functions
- 솔팅(Salting)과 키 스트레칭(key stretching)을 반복하고, 솔트와 패스워드 외에도 입력 값을 추가하여 공격자가 쉽게 다이제스트를 유추할 수 없도록 하는 방식 
- 최근에는 라이브러리로 구현되어 사용할 수 있음 
- 주요한 key derivation function
  - PBKDF2
  - bcrypt
  - scrypt

### 예시 (PBKDF2)
```
DIGEST = PBKDF2(PRF, Password, Salt, c, DLen)  
```
- 파라미터
	- PRF: 의사 난수 생성 함수 (솔트 생성 위함)
	- Password: 패스워드
	- Salt: 암호학 솔트
	- c: 원하는 iteration 반복 수
	- DLen: 원하는 다이제스트 길이
- [PBKDF2 암호 생성하기](http://cris.joongbu.ac.kr/course/2019-1/wp/crypto/symmetric2.html)
- [Django가 Password를 생성하는 방법](https://www.kimsungyoo.com/how-django-create-password/)

<br></br>
# 2. 클린코드 
## 1)클린코드를 만드는 규칙들
> [클린코드 Part 1](http://www.kosta.or.kr/mail/2015/download/CleanCode_Part1.pdf) </br>
> [클린코드 Part 2](http://kosta.or.kr/mail/2015/download/CleanCode_Part2.pdf) </br>
- **(1) 의미있는 이름 (Naming)**
	- 코드 상에 나만 아는 숫자를 쓴다든지 추상적인 변수명으로 무엇을 하는지 알 수 없는 이름도 사용하지 않도록 한다. 
	- do( ), process( )와 같이 너무 일반적인 이름보다는 이름만으로도 언제 이 메서드를 호출해야 하는지 의미를 파악할 수 있도록 구체적으로 작성하도록 한다.
  	- 한 단어를 여러 목적으로 사용하는 것도 피하도록 한다. 
  
- **(2) 명확하고 간결하게 주석달기 (Comment)**
	- 주석이 필요한 경우 최대한 간결하고 명확하게 작성하여 읽는데 많은 시간을 사용하지 않도록 주의한다. 간혹 필요 없는 주석이나 이상한 주석은 코드를 읽는 것을 더 어렵게 하는 경우 도 있기 때문이다. 
	- 코드 네이밍을 구체적이고 명확하게 하는 것이 주석이 필요하지 않도록 만드는 좋은 방법이다.  
- **(3) 보기좋게 배치하고 꾸미기 (Aesthetics)**
	- 코드에서도 읽는 사람이 편하게 읽을 수 있도록 구성하는 것이 중요하다. 
	- 아래 데이터베이스 기능을 테스트하기 위한 예제코드를 보면 코드가 지저분하고 읽기가 어렵다. 
		- 코드 중간에 반복되는 내용과 줄이 넘어가면서 한눈에 코드가 보이지 않는다. 
		- 코드에서 반복되는 ExpandFullName(database_ connection, …) 구문은 새로운 함수로 선언하여 정리하고 배열을 정리해보자. 이는 리팩토링에서 사용되는 방법이기도 하다.
	- 수정 전
	``` java
	DatabaseConnection database_connection;
	string error;
	assert(ExpandFullName(database_connection, "Doug Adems", &error) == "Mr. Douglas Adams");
	assert(error=="");
	assert(ExpandFullName(database_connection, "Jake Brown", &error) == "Mr. Jacob Brown III");
	assert(error=="");
	assert(ExpandFullName(database_connection, "No Such Guy", &error) == "");
	assert(error=="no match found");
	assert(ExpandFullName(database_connection, "John", &error) == "");
	assert(error=="more than one result");
	```

	- 수정 후 
	``` java
	CheckFullName("Doung Adams", "Mr. Douglas Adams", "");
	CheckFullName(" Jake Brown ", "Mr.Jake Brown III", "");
	CheckFullName("No Such Guy", "", "no atch found");
	CheckFullName("John", "", "more than one result");

	void ChckFullName(string partial_name, string expected_full_name, string expected_error) {
		// database_connection is now a class member
		string error;
		string full_name = ExpandFullName(dataase_connection, partial_name, &error);
		assert(error == expected_error);
		assert(full_name == expected_full_name);
	}
	```
- **(4) 착한 함수 (Function)**
	- 함수를 만드는 원칙은 가급적 작게 만들어야 한다. 
	- if문이나 While문 안의 내용은 한줄로 처리되도록 하는 것이 이상적이고 블록 안에서 다른 함수를 호출하도록 작성한다.
	- 함수의 크기는 20줄 이내, 한라인당 150문자를 넘지 않도록 한다.  

- **(5) 읽기 쉽게 흐름제어 만들기 (Making control flow easy to read)**
	- if/else 조건문에서 인수의 순서는 긍정적이고, 간단한 표현이 앞쪽에 위치하도록 하는 것이다.  
	- 삼항연산자(? :)나 do/while, 혹은 goto 구문은 코드의 가독성을 떨어뜨리기 때문에 되도록 사용하지 않는 것이 바람직하다.  
		- 삼항연산자보다 if 문으로 작성하는 편이 자연스럽다. 
		- do/while의 경우는 while문으로 변경하여 코드블록을 보기 전에 반복되는 조건을 미리 확인할 수 있도록 하는 것이 바람직하다. 



## 2) 클린코드 예시

> [카카오헤어샵](https://brunch.co.kr/@cg4jins/2)
### (1) 의미있는 이름 (Naming)

- **준말(약어)은 지양**
	- Product를 prod로 쓰지 않고 Reservation을 rsv로 쓰지 않음
	- 코드값도 'S', 'R' 형태를 사용하지 않고 'Stopped', 'Ready'와 같이 본말을 그대로 사용 
- **데이터와 응용 클래스 분리**
	- getter/setter는 Entity, Value Object, Date Transger Object와 같이 필드만 있고 operation이 없는 데이터 클래스에서만  get, set 함수명을 사용
	- 오퍼레이션만 있는 응용 클래스는 findByShopId(), createShopOne(), addItemToList()처럼 다른 형태의 동사를 사용
- **클래스명은 명사로**
	- CreateProductListService, InsertShopSearchBatch, MainDataSourceInitializer 형태로 작성
- **데이터의 필드명에 클래스명을 중복으로 사용하지 않음**
	- product.productName (x) -> product.name (o)


### (2) 착한 함수 (Function)
- **함수는 4줄이 넘지 않도록 작성**
	- 코드가 길어질 경우 외부 클래스에 위임하고 함수는 가능하면 4줄이 넘기지 않도록 함
	- 일관된 추상화 수준으로 일관된 추상화 수준으로 함수를 분리
	- 틀린 쓰임) https://gist.github.com/cg4jins/764ca0c1107322e072f87bcea5d789b4
	- 맞는 쓰임) https://gist.github.com/cg4jins/aa8bcceb79d3ad75966e96443652249b
- **if문은 return과 함께 쓰고 else는 지양**
	- if 문 안에 복잡한 조건식이 나오지 않게 함. if 문이 나오면 return 하고 그 아래 코드를 작성함.
	- Example
	``` C
	if (상황 1)
	    return;

	doA();
	doB();
	``` 
- **함수 인자는 1개**
	- 함수의 인자는 가능하면 1개의 클래스로 하는 것을 지향
	- 여러 개의 인자가 필요할 경우 클래스를 새로 정의해서 사용
- **null을 반환하거나 null을 전달하지 않도록 함수 작성**
	- null을 잘못 사용하는 것은 NullPointerException을 유발할 수 있음. 따라서 함수에서 null을 반환하거나 전달하지 않게 함.

### (3) 명확하고 간결하게 주석달기 (Comment)

- **안 쓰는 소스코드는 주석처리 대신 반드시 삭제**
- **주석 대신 signature로 대체**
	- 주석 내용을 메쏘드명, 변수명에 최대한 녹일 수 있도록 했습니다.


### (4) 객체
- **생성자 대신 정적 팩토리 메쏘드**
	- 비즈니스 로직에서 객체를 new 해서 생성하지 않게 했습니다. 반드시 Factory를 만들어 정적 메쏘드로 생성하게 했습니다.
	- 생성자를 직접 사용하지 않은 이유
		- 생성자는 이름이 없어서 의도를 알 수 없어서 readablity가 떨어짐.
		- 생성 후 다른 클래스의 인자로 넘기고 그 안에서 다시 set을 하는 것을 방지할 수 있음.

- **객체 생성을 막을 때는 private 생성자 지정**
	- 생성자 대신 정적 팩토리 메쏘드를 사용한 곳은 private 생성자를 지정하여 외부에서 new로 객체를 생성할 수 없게 함. 
	- 이는 해당 객체를 생성하는 것은 반드시 정적 팩토리 메쏘드를 사용해야만 한다는 것을 알려주는 좋은 방법임.

- **클래스는 메쏘드 4개 이하로.**
	- 클래스 1개의 코드가 너무 긴 경우를 방지하기 위함.



## 3) 리팩토링
- 프로그램의 외부 동작은 그대로 둔 채, 내부의 코드를 정리하면서 개선하는 것
- 가독성을 높이고 유지보수를 용이하게 하기 위함
- 리팩토링이 필요한 코드
  - 중복 코드
  - 긴 메소드
  - 거대한 클래스
  - 절차지향으로 구현한 코드
  - Switch 문 : 객체지향 특징을 살리기 위해서는 switch-case 문을 적게 사용해야 함 (switch문은 오버라이드로 다 바꾸자)

  
## 4) 리팩토링 예시
### (1) 함수와 파라미터가 모호한 경우 
- 수정 전
``` C++
public int getFoodPrice(int arg1, int arg2){
  return arg1 * arg2;
}
```

- 수정 후
  - 함수 명, 파라미터를 직관적으로 수정 
```C++
public int getTotalFoodPrice(int price, int quantity) {
  return price *quantity;
}
```

### (2) 중복 코드, 긴 메소드
- 수정 전
```C++
public int getTotalFoodPrice(int price, int quantity, double discount) {
  return (int) ((price*quantity)*(price*quantity)*(discount/100));
}
```

- 수정 후 (1)
  - 중복되는 부분은 변수로 추출
  - 할인율 계산하는 부분을 메소드로 추출
  - 할인율 계산 함수는 항상 일정하므로 private 선언
``` C++
public int getTotalFoodPrice(int price, int quantity, double discount){
  int totalPriceQuantity = price * quantity;
  return (int) (totalPriceQuantity - getDiscountPrice(discount, totalPriceQuantity))
}

private double getDiscountPrice(double discount, int totalPriceQuantity) {
  return totalPriceQuantity * (discount/100);
}
```

- 수정 후 (2)
  -  totalPriceQuantity를 getter 메소드로 추출 
``` C++
public int getTotalFoodPrice(int price, int quantity, double discount){
  int totalPriceQuantity = getTotalPriceQuantity(price, quantity);
  return (int) (totalPriceQuantity - getDiscountPrice(discount, totalPriceQuantity))
}

private double getDiscountPrice(double discount, int totalPriceQuantity) {
  return totalPriceQuantity * (discount/100);
}

private int getTotalPriceQuantity(int price, int quantity) {
  return price * quantity;
}
```

### (3) switch문
- 수정 전
``` java
public static final int CHILDREN = 2;
public static final int REGULAR = 0;
public static final int NEW_RELEASE = 1;

swtich (getPriceCode()) {
  case Regular :
    // do something
    break;
  case NEW_RELEASE :
    // do something
    break;
  case CHILDREN :
    // do something
    break;
 }
```

- 수정 후
``` java
public abstract class Price {
  abstract int getPriceCode();
  abstract double getCHarge(int daysRented);
  public int getFrequentRenterPoints(int daysRented) {
      return 1;
  }
}

public class ChildrenPrice extends Price{

	@Override
	int getPriceCode() {
		return Movie.CHILDREN;
	}

	@Override
	double getCharge(int daysRented) {
		double result = 1.5;
		if(daysRented > 3)result += (daysRented-3)*1.5;
		return result;
	}
}

public class NewReleasePrice extends Price {

	@Override
	int getPriceCode() {
		return Movie.NEW_RELEASE;
	}

	@Override
	double getCharge(int daysRented) {
		return daysRented * 3;
	}

	@Override
	public int getFrequentRenterPoints(int daysRented) {
		return (daysRented > 1) ? 2 : 1;
	}

}

public class RegularPrice extends Price{

	@Override
	int getPriceCode() {
		return Movie.REGULAR;
	}

	@Override
	double getCharge(int daysRented) {
		double result = 2;
		if(daysRented >3)result += (daysRented -2)*1.5;
		return result;
	}

}
```

<br></br>
 
# 3. TDD (Test-Driven-Development) 

## 1) 정의
- 테스트 케이스 작성으로 구현을 시작하는 것
- ‘제대로 동작(works)’하면서 ‘깔끔함 (clean)’까지 동등한 수준의 개발 목표로 삼는다는 점이 일반적인 개발 방식과 다르다. 
- TDD에서 말하는 단위 테스트는 일반적으로 메소드 단위의 테스트를 뜻함</br>
*추후 추가.....*


 

<br></br>
<br></br>
> Reference</br>
> 암호화</br>
> [안전한 패스워드 저장](https://d2.naver.com/helloworld/318732)
> [해시값의 복호화](https://jusungpark.tistory.com/35)</br>
> https://seed.kisa.or.kr/kisa/intro/EgovDefinition.do</br>
> [ITWorld 용어풀이 | 암호화 해시 함수](https://www.itworld.co.kr/news/94202)</br>
> https://kim6394.tistory.com/213</br>
> [리팩토링](https://thekizel.tistory.com/entry/Refactorying-%EC%B2%AB%EB%B2%88%EC%A7%B8-%EC%98%88%EC%A0%9C%EC%97%90%EC%84%9C-%EC%9D%B8%EC%83%81-%EA%B9%8A%EC%97%88%EB%8D%98-%EC%BD%94%EB%93%9C)</br>
> [TDD(Test-Driven-Development) 방법론에 대해서](https://wooaoe.tistory.com/33)</br>
> [TDD 실천법과 도구](https://repo.yona.io/doortts/blog/issue/2) 
