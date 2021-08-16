# Generic

## 제네릭이란?

> 데이터의 타입(data type)을 일반화(Generalize)하는 것

클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법

컴파일 시에 미리 타입 검사를 수행하면

- 클래스나 메소드 내부에서 사용되는 객체의 타입 안정성을 높일 수 있다.
- 반환값에 대한 타입 변환 및 타입 검사에 들어가는 노력을 줄일 수 있다.

자바에서 제네릭은 JDK 1.5부터 추가된 기능



## 제네릭을 사용하는 이유

- 잘못된 타입이 사용될 수 있는 문제를 **컴파일 과정** 에서 제거할 수 있다
- 자바 컴파일러는 코드에서 잘못 사용된 타입 때문에 발생하는 문제점을 제거하기 위해 제네릭 코드에 대한 강한 타입 체크를 한다
- 실행 시 타입 에러가 나는 것보다 **컴파일 시**에 타입 체크를 강하게 해서 에러를 사전에 방지하는 것이 좋다



```java
List list = new ArrayList();
list.add("Hello World");
String s = (String)list.get(0); // 타입 캐스팅
```

제네릭을 사용하면 캐스팅을 하지 않아도 됨

```java
List<String> list = new ArrayList<String>();
list.add("Hello World");
String s = list.get(0);
```

제네릭을 사용하여 다양한 자료형의 컬렉션에서 작용, type safe한 코드를 작성할 수 있음



## 제네릭 사용법

#### 제네릭 선언 및 생성

```java
class Gen<T> {
    T gen;
    T getGeneric{
        return gen;
    }
    void setGeneric(T gen){
        this.gen = gen;
    }
}
```

여기서 **T**를 ``타입 변수`` 라고 한다. T말고 어떤것을 사용해도 상관없지만 Type을 나타내는 "약속"임. f(x,y) = 1 이나 f(a,b)=1 이나 같듯이.

|  타입   |     의미      |
| :-----: | :-----------: |
|    T    |     Type      |
|    E    |    Element    |
|    N    |    Number     |
|    K    |      Key      |
|    V    |     Value     |
| S, U, V | 2nd, 3rd, 4th |



## 제네릭 개념

#### 바운디드 타입

> 제네릭으로 사용될 타입 파라미터의 값을 제한할 수 있는 방법이다.

```java
// 숫자값이라면 모두 사용가능
public class GenericClass<T extends Number> { }

public static void main(String[] args){
    GenericClass<Integer> integerGeneric = new GenericClass<Integer>();
    GenericClass<Float> integerGeneric = new GenericClass<Float>();
    GenericClass<Long> integerGeneric = new GenericClass<Long>();
    GenericClass<String> integerGeneric = new GenericClass<String>(); // 컴파일 에러
}
```



#### 와일드 카드

> 제네릭으로 구현된 메소드의 경우 선언된 타입으로만 매개변수를 입력해야 한다.  상속받은 클래스 혹은 부모 클래스를 사용하고 싶어도 불가능하고, 어떤 타입이 와도 상관없는 경우 좋지 않은데, 이 때 WildCard를 사용한다

- <? super T> - Upper Bounded: 와일드 카드의 상한 제한. T와 그 조상들만 가능
- <? extends T>  - Lower Bounded: 와일드 카드의 하한 제한. T와 그 자식들만 가능
- <?> - Unbounded : 제한 없이 모든 타입 가능. <? extends Object> 와 동일하다.

```java
public class Person {}

public class Student extends Person {}

public static void main(String[] args){
    List<? super Person> p = new ArrayList<Person>();
    List<? super Person> p = new ArrayList<Student>(); // 컴파일 에러
    
    List<? extends Person> p = new ArrayList<Student>();
    List<? extends Person> p = new ArrayList<Person>();
}
```



#### 제네릭 메소드

```java
// Collections 클래스의 sort 메서드
public static <T> void sort(List<T> list, Comparator<? super T> c) {
    list.sort(c);
}
```



#### 제네릭 타입 제거

제네릭을 사용하면 그 타입은 컴파일 후 지워지게 된다.

컴파일 시에는 타입 제약이 있지만, 런타임시에는 제약이 사라진다는 뜻

1. 제네릭 타입에서 해당 타입 파라미터(T)나 Object로 변경시켜줌. 

2. 타입 안정성 보존을 위해 type casting

3. 확장된 제네릭 타입에서 다형성을 보존하기 위해 bridge method 생성

   ```java
   public static <E> boolean containsElement(E[] elements, E element){
       for(E e : elements){
           if(e.equals(element)){
               return true;
           }
       }
       return false;
   }
   ```

4. 실제로 이렇게 선언되어있는 메서드 경우 선언 방식에 따라 컴파일러가 실제 유형의 Object로 변경

   ```java
   public static boolean containsElement(Object [] elements, E element){
       for(Object e : elements){
           if(e.equals(element)){
               return true;
           }
       }
       return false;
   }
   ```







## Ref

- https://docs.oracle.com/javase/tutorial/extra/generics
- 
