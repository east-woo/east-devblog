---
title: "[JAVA] Optional"
description: "어떻게 글을 작성하고 추가할까요?"
date: 2024-09-02
update: 2024-09-02
tags:
  - JAVA
series: "JAVA"
---
# **1. Optional 개념**

## **NPE(NullPointerException) 이란?**

개발을 할 때 가장 많이 발생하는 예외 중 하나가 바로 NPE(NullPointerException)이다. NPE를 피하려면 null 여부를 검사해야 하는데, null 검사를 해야하는 변수가 많은 경우 코드가 복잡해지고 번거롭다. 그래서 null 대신 초기값을 사용하길 권장하기도 한다.

```java
List<String> names = getNames();
names.sort(); // names가 null이라면 NPE가 발생함

List<String> names = getNames();
// NPE를 방지하기 위해 null 검사를 해야함
if(names != null){
    names.sort();
}
```

## **Optional이란?**

Java8에서는 Optional<T> 클래스를 사용해 NPE를 방지할 수 있도록 도와준다. Optional<T>는 null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NPE가 발생하지 않도록 도와준다. Optional 클래스는 아래와 같은 value에 값을 저장하기 때문에 값이 null이더라도 바로 NPE가 발생하지 않으며, 클래스이기 때문에 각종 메소드를 제공해준다.

```java
public final class Optional<T> {

  // If non-null, the value; if null, indicates no value is present
  private final T value;
   
  ...
}
```

---

# **2. Optional 활용하기**

## **Optional 생성하기**

### **Optional.empty() - 값이 Null인 경우**

Optional은 Wrapper 클래스이기 때문에 값이 없을 수도 있는데, 이때는 Optional.empty()로 생성할 수 있다.

```java
Optional<String> optional = Optional.empty();

System.out.println(optional); // Optional.empty
System.out.println(optional.isPresent()); // false
```

Optional 클래스는 내부에서 static 변수로 EMPTY 객체를 미리 생성해서 가지고 있다. 이러한 이유로 빈 객체를 여러 번 생성해줘야 하는 경우에도 1개의 EMPTY 객체를 공유함으로써 메모리를 절약하고 있다.

```java
public final class Optional<T> {

    private static final Optional<?> EMPTY = new Optional<>();
    private final T value;
    
    private Optional() {
        this.value = null;
    }

    ...

}
```

### **Optional.of() - 값이 Null이 아닌 경우**

만약 어떤 데이터가 절대 null이 아니라면 Optional.of()로 생성할 수 있다. 만약 Optional.of()로 Null을 저장하려고 하면 NullPointerException이 발생한다.

```java
// Optional의 value는 절대 null이 아니다.
Optional<String> optional = Optional.of("MyName");
```

### **Optional.ofNullbale() - 값이 Null일수도, 아닐수도 있는 경우**

만약 어떤 데이터가 null이 올 수도 있고 아닐 수도 있는 경우에는 Optional.ofNullbale로 생성할 수 있다. 그리고 이후에 orElse 또는 orElseGet 메소드를 이용해서 값이 없는 경우라도 안전하게 값을 가져올 수 있다.

```java
// Optional의 value는 값이 있을 수도 있고 null 일 수도 있다.
Optional<String> optional = Optional.ofNullable(getName());
String name = optional.orElse("anonymous"); // 값이 없다면 "anonymous" 를 리턴
```

## **Optional 사용법 예시 코드**

### **Optional 사용법 예시 (1)**

기존에는 아래와 같이 null 검사를 한 후에 null일 경우에는 새로운 객체를 생성해주어야 했다. 이러한 과정을 코드로 나타내면 다소 번잡해보이는데, Optional<T>와 Lambda를 이용하면 해당 과정을 보다 간단하게 표현할 수 있다.

```java
// Java8 이전
List<String> names = getNames();
List<String> tempNames = list != null 
    ? list 
    : new ArrayList<>();

// Java8 이후
List<String> nameList = Optional.ofNullable(getNames())
    .orElseGet(() -> new ArrayList<>());
```

### **Optional 사용법 예시 (2)**

예를 들어 아래와 같은 우편번호를 꺼내는 null 검사 코드가 있다고 하자. 이 코드는 null 검사 때문에 상당히 복잡하다.

```java
public String findPostCode() {
    UserVO userVO = getUser();
    if (userVO != null) {
        Address address = user.getAddress();
        if (address != null) {
            String postCode = address.getPostCode();
            if (postCode != null) {
                return postCode;
            }
        }
    }
    return "우편번호 없음";
}
```

하지만 Optional을 사용하면 이러한 코드를 아래와 같이 표현할 수 있다.

```java
public String findPostCode() {
// 위의 코드를 Optional로 펼쳐놓으면 아래와 같다.
    Optional<UserVO> userVO = Optional.ofNullable(getUser());
    Optional<Address> address = userVO.map(UserVO::getAddress);
    Optional<String> postCode = address.map(Address::getPostCode);
    String result = postCode.orElse("우편번호 없음");

// 그리고 위의 코드를 다음과 같이 축약해서 쓸 수 있다.String result = user.map(UserVO::getAddress)
        .map(Address::getPostCode)
        .orElse("우편번호 없음");
}
```

### **Optional 사용법 예시 (3)**

예를 들어 아래와 같이 이름을 대문자로 변경하는 코드에서 NPE 처리를 해준다고 하자.

```java
String name = getName();
String result = "";

try {
    result = name.toUpperCase();
} catch (NullPointerException e) {
    throw new CustomUpperCaseException();
}
```

위의 코드는 다소 번잡하고 가독성이 떨어지는데 이를 Optional를 활용하면 아래와 같이 표현할 수 있다.

```java
Optional<String> nameOpt = Optional.ofNullable(getName());
String result = nameOpt.orElseThrow(CustomUpperCaseExcpetion::new)
                  .toUpperCase();
```

## **Optional 정리**

Optional은 null 또는 값을 감싸서 NPE(NullPointerException)로부터 부담을 줄이기 위해 등장한 Wrapper 클래스이다. Optional은 값을 Wrapping하고 다시 풀고, null 일 경우에는 대체하는 함수를 호출하는 등의 오버헤드가 있으므로 잘못 사용하면 시스템 성능이 저하된다. 그렇기 때문에 메소드의 반환 값이 절대 null이 아니라면 Optional을 사용하지 않는 것이 좋다. 즉, Optional은 메소드의 결과가 null이 될 수 있으며, null에 의해 오류가 발생할 가능성이 매우 높을 때 반환값으로만 사용되어야 한다. 또한 Optional은 파라미터로 넘어가는 등이 아니라 반환 타입으로써 제한적으로 사용되도록 설계되었는데, 이것은 이어지는 포스팅에서 살펴보도록 하자.

# **3. Optional의 orElse와 orElseGet 차이 및 예시 코드**

## **Optional의 orElse와 orElseGet 차이 및 예시 코드**

### **orElse와 orElseGet의 차이**

Optional API의 단말 연산에는 orElse와 orElseGet 함수가 있다. 비슷해 보이는 두 함수는 엄청난 차이가 있다.

- orElse: 파라미터로 값을 받는다.
- orElseGet: 파라미터로 함수형 인터페이스(함수)를 받는다.

실제로 Optional 코드를 보면 다음과 orElse와 orElseGet이 각각 구현되어 있음을 확인할 수 있다.

```java
public final class Optional<T> {

    ...// 생략public T orElse(T other) {
        return value != null ? value : other;
    }

    public T orElseGet(Supplier<? extends T> other) {
        return value != null ? value : other.get();
    }

}
```

orElse로는 값이, orElseGet로는 함수가 넘어간다는 것은 상당히 큰 차이가 있다. 이로 인해 호출 결과가 달라질 수 있기 때문인데, 관련된 내용을 코드로 살펴보도록 하자.

### **orElse와 orElseGet의 차이 예시 코드**

예를 들어 다음과 같은 예시 코드가 있다고 하자. 첫 번째 함수는 값이 비어있을 때 orElse를 호출하도록 되어있고, 두 번째 함수는 orElseGet을 호출하도록 되어있다. 바로 아래로 내려가지 않고, 각각에 의한 출력 결과를 예상해보도록 하자.

```java
public void findUserEmailOrElse() {
    String userEmail = "Empty";
    String result = Optional.ofNullable(userEmail)
    	.orElse(getUserEmail());

    System.out.println(result);
}

public void findUserEmailOrElseGet() {
    String userEmail = "Empty";
    String result = Optional.ofNullable(userEmail)
    	.orElseGet(this::getUserEmail);

    System.out.println(result);
}

private String getUserEmail() {
    System.out.println("getUserEmail() Called");
    return "mangkyu@tistory.com";
}
```

위의 함수를 각각 실행해보면 다음과 같은데, 이러한 결과가 발생한 이유를 자세히 살펴보도록 하자.

```java
// 1. orElse인 경우
getUserEmail() Called
Empty

// 2. orElseGet인 경우
Empty
```

먼저 OrElse인 경우에는 다음과 같은 순서로 처리가 된다.

1. Optional.ofNullable로 "EMPTY"를 갖는 Optional 객체 생성
2. getUserEmail()가 실행되어 반환값을 orElse 파라미터로 전달
3. orElse가 호출됨, "EMPTY"가 Null이 아니므로 "EMPTY"를 그대로 가짐

위와 같이 동작하는 이유는 Optional.orElse()가 값을 파라미터로 받고, orElse 파라미터로 값을 넘겨주기 위해 getUserEmail()이 호출되었기 때문이다. 하지만 함수형 인터페이스(함수)를 파라미터로 받는 orElseGet에서는 동작이 달라진다.

1. Optional.ofNullable로 "EMPTY"를 갖는 Optional 객체 생성
2. getUserEmail() 함수 자체를 orElseGet 파라미터로 전달
3. orElseGet이 호출됨, "EMPTY"가 Null이 아니므로 "EMPTY"를 그대로 가지며 getUserEmail()이 호출되지 않음

orElseGet에서는 파라미터로 넘어간 값인 getUserEmail 함수가 Null이 아니므로 .get에 의해 함수가 호출되지 않는다. 만약 Optional의 값으로 null이 있다면, 다음과 같은 흐름에 의해 orElseGet의 파라미터로 넘어온 getUserEmail()이 실행될 것이다.

```java
public void findUserEmailOrElseGet() {
    String result = Optional.ofNullable(null)
    	.orElseGet(this::getUserEmail);

    System.out.println(result);
}

private String getUserEmail() {
    System.out.println("getUserEmail() Called");
    return "mangkyu@tistory.com";
}
```

1. Optional.ofNullable로 null를 갖는 Optional 객체 생성
2. getUserEmail() 자체를 orElseGet 파라미터로 전달
3. orElseGet이 호출됨, 값이 Null이므로 other.get()이 호출되어 getUserEmail()가 호출됨

### **orElse에 의한 발생가능한 장애 예시**

위에서 살펴보았듯 orElse와 orElseGet은 명확하고 중요한 차이가 있는데, 이를 정확히 인식하지 못하면 장애가 발생할 수 있다. 예를 들어 userEmail을 Unique한 값으로 갖는 시스템에서 아래와 같은 코드를 작성하였다고 하자.

```java
public void findByUserEmail(String userEmail) {
// orElse에 의해 userEmail이 이미 존재해도 유저 생성 함수가 호출되어 에러 발생return userRepository.findByUserEmail(userEmail)
            .orElse(createUserWithEmail(userEmail));
}

private String createUserWithEmail(String userEmail) {
    User newUser = new User(userEmail);
    return userRepository.save(newUser);
}
```

위의 예제는 Optional의 단말 연산으로 orElse를 사용하고 있기 때문에, 조회 결과와 무관하게 createUserWithEmail 함수가 반드시 실행된다. 하지만 Database에서는 userEmail이 Unique로 설정되어 있기 때문에 오류가 발생할 것이다. 그렇기 때문에 위와 같은 경우에는 다음과 같이 해당 코드를 orElseGet으로 수정해야 한다. 이렇게 코드를 수정하였다면 파라미터로 createUserWithEmail 함수 자체가 넘어가므로, 조회 결과가 없을 경우에만 사용자를 생성하는 로직이 호출 될 것이다.

```java
public void findByUserEmail(String userEmail) {
// orElseGet에 의해 파라미터로 함수를 넘겨주므로 Null이 아니면 유저 생성 함수가 호출되지 않음return userRepository.findByUserEmail(userEmail)
           .orElseGet(this::createUserWithEmail(userEmail));
}

private String createUserWithEmail(String userEmail) {
    User newUser = new User(userEmail);
    return userRepository.save(newUser);
}
```

실제 서비스에서 위와 같은 오류를 범한다면 큰 시스템 장애로 돌아오게 된다. 설령 문제가 없다고 하더라도 orElse는 값을 생성하여 orElseGet보다 비용이 크므로 최대한 사용을 피해야 한다. 그러므로 orElse와 orElseGet의 차이점을 정확히 이해하고 사용하도록 하자.

### **orElse와 orElseGet의 차이점 및 사용법 정리**

- orElse
    - 파라미터로 값을 필요로한다.
    - 값이 미리 존재하는 경우에 사용한다.
- orElseGet
    - 파라미터로 함수(함수형 인터페이스)를 필요로 한다.
    - 값이 미리 존재하지 않는 거의 대부분의 경우에 orElseGet을 사용하면 된다.

Optional은 상당히 유연하고 Null로부터 안전을 보장받을 수 있어서 좋아 보입니다. 하지만 잘못된 Optional의 사용은 커다란 비용을 요구하며 많은 단점들을 야기할 수 있습니다. Java 언어의 아키텍트(설계자)인 Brian Goetz는 Optional에 대해 다음과 같이 정의하였습니다.

> Optional is intended to provide a limited mechanism for library method return types where there needed to be a clear way to represent “no result," and using null for such was overwhelmingly likely to cause errors.
- Brian Goetz(Java Architect) -
>

즉, 위의 내용을 매우 간추리면 Optional은 반환 타입으로 사용되도록 매우 제한적인 경우로 설계되었다는 것인데, 다음 포스팅에서는 언제 Optional을 사용해야 하는지, 어떻게 사용해야 하는지에 대해 알아보도록 하겠습니다.

참고

- https://mangkyu.tistory.com/70