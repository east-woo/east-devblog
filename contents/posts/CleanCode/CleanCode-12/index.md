---
title: "[클린코드-12] 동시성을 구현할 때 명심할 것들"
description: "어떻게 글을 작성하고 추가할까요?"
date: 2024-07-05
update: 2024-07-05
tags:
  - 독서
  - Clean Code
series: "Clean Code"
---

**Chapter 13. 동시성**<br>
**page 225 ~ 244**<br>
**부록 407 ~ 466**

# 동시성 프로그래밍이란

- **어플리케이션을 효율적으로 실행하기 위해 멀티코어를 온전히 활용하도록 구현하는 방식**
- 외부 서비스의 응답을 기다리면서 아무일도 하지 않으면 CPU 사이클이 낭비된다
- 낭비되는 자원을 줄이자

## 동시성 프로그래밍 이해하기

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/9b591b10-f2a5-473b-8022-8a11ddd6a947/Untitled.png)

## 동시성이 구현되지 않는 경우

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/15bbf121-6fb4-46ff-9405-a439c6a38703/Untitled.png)

서버(코어)를 늘려 처리량을 높여보자!

## 병렬성을 구현한 경우

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/83f6147b-c700-46cc-9350-1465853e13fb/Untitled.png)

CPU 클럭 낭비!!

## 동시성을 구현한 경우

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/1b65681d-3478-492d-8e11-3aaf21a72a06/Untitled.png)

## 동시성과 병렬성이 구현된 경우

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/11ac1bed-2c2e-491b-8338-396751a6a3df/Untitled.png)

서버(코어)를 효율적으로 사용하여 처리량을 최대화했다.

클라이언트가 아닌 어플리케이션 관점에서 봐야한다.

내 어플리케이션의 효율성을 높여야 한다.
더불어 내 어플리케이션이 동작하는 머신의 환경이 효율적으로 돌아가도록
내 어플리케이션에 메모리 누수나 자원이 낭비되지 않도록 신경쓴다.

# 동시성 프로그래밍이 필요한 이유

## 동시성 프로그래밍의 미신과 오해 (1)

**동시성은 항상 성능을 높여준다 (X)
동시성은 때로 성능을 높여준다 (O)**

- 대기 시간이 아주 길어 여러 스레드가 프로세서를 공유할 수 있거나, 여러 프로세서가 동시에 처리할 독립적인 계산이 충분히 많은 경우에만 성능이 높아진다.
- 예시: 웹 브라우저에서 여러 가지 이미지 리소스들을 불러와 다운로드할때
- 예시 상황: ServIet

## Java ServIet 동시성 구현

![https://medium.com/@wminikuma/spring-spring-5-mvc-with-java-based-configuration-fdf3cba27dae](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/64418a5a-3817-4dca-b2d8-44625f57019d/Untitled.png)

https://medium.com/@wminikuma/spring-spring-5-mvc-with-java-based-configuration-fdf3cba27dae

- 요청이 들어오면 Thread Pool에 있는 Thread가 서블릿의 service() 메서드를 호출한다.
- service의 doGet(), doPost()에서 요청에 대한 처리를 하도록 구현한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/e2526eb7-0126-42a2-a94c-67a24c1080db/Untitled.png)

```java
public class SimpleHttpServlet extends HttpServlet {

  // Not thread safe, static.
  protected static List list = new ArrayList();

  // Not thread safe
  protected Map map = new HashMap();

  // Thread safe to access object, not thread safe to reassign variable.
  protected Map map = new ConcurrentHashMap();

  // Thread safe to access object (immutable), not thread safe to reassign variable.
  protected String aString = "a string value";

  protected void doGet( HttpServletRequest request,
                        HttpServletResponse response)
        throws ServletException, IOException {

    // Not thread safe, unless the singleton is 100% thread safe.
    SomeClass.getSomeStaticSingleton();

    // Thread safe, locally instantiated, and never escapes method.
    Set set = new HashSet();

  }
}

```

1. 해당 서블릿에 여러 쓰레드가 접근할 수 있기 때문에 멤버변수들은 Not Thread Safe 하다.
2. doGet(), doPost()는 하나의 쓰레드 영역에서 실행되기 때문에 로컬 변수들은 Thread Safe 하다

ConcurrentHashMap→ 접근에 대해서는 Thread Safe, 하지만 값을 재 할당 하는 경우는 Thread Safe 하지 않음

## 동시성 프로그래밍의 미신과 오해 (2)

**동시성을 구현해도 설계는 변하지 않는다 (X)
동시성을 구현하면 설계를 바꿔야 한다(O)**

- 단일 스레드 시스템과 다중 스레드 시스템은 설계가 판이 하게 다르다.
- '무엇'과 '언제를 분리하면 시스템의 구조가 크게 달라진다.

## 동시성 프로그래밍의 미신과 오해 (3)

**Web나 EJB와 같은 컨테이너를 사용한다면 동시성을 이해할 필요가 없다(X)
컨테이너를 사용해도 동시성을 이해해야 한다(O)**

- 어플리케이션이 컨테이너를 통해 멀티 쓰레드를 사용하는 것이기 때문에 컨테이너의 동작을 이해해야 한다.
- 동시 수정, 데드락 같은 문제를 피할 수 있는지를 알아야 한다.

# 안전한 동시성 프로그래밍 규칙

## 단일 책임 원칙 설계(SRP)

### 동시성 관련 코드는 다른 코드와 분리하라

- 동시성 코드는 독자적인 개발, 변경, 조율 주기가 있다.
- 동시성 코드에는 독자적인 난관이 있다. 다른 코드에서 겪는 난관과 다르며 훨씬 어렵다.
- 잘못 구현한 동시성 코드는 별의별 방식으로 실패한다.
  주변에 있는 다른 코드가 발목을 잡지 않더라도 동시성 하나만으로도 충분히 어렵다

## 자료 범위를 제한하라

### 공유 자료를 최대한 줄여라

- 동시 수정 문제를 피하기 위해 객체를 사용하는 코드 내 임계영역을 synchronized 키워드로 보호하라
- 보호할 임계영역을 빼먹거나, 모든 임계영역을 보호했는지 확인하느라 수고가 드므로 임계 영역의 수를 최소화 해야 한다. (임계영역 작게 만들고, 적게 만들어야 한다.)

## 자료 사본을 사용하라

### 공유 자료를 줄이려면, 최대한 공유하지 않는 방법이 제일 좋다. (각각 로컬변수로 활용)

- 객체를 복사해 읽기 전용으로 사용한다.
- 각 스레드가 객체를 복사해 사용한 후 한 스레드가 해당 사본에서 결과를 가져온다.
- 사본을 사용하는 방식으로 내부 잠금을 없애 수행 시간을 절약하는 것이
  사본 생성과 가비지 컬렉션에 드는 부하를 상쇄할 가능성이 크다.

## Thread는 가능한 독립적으로 구현하라

### 다른 스레드와 자료를 공유하지 않는다.

- 서블릿처럼 각 Thread는 클라이언트 요청 하나를 처리한다.
- 모든 정보는 비공유 출처(cIient의 request)에서 가져오며 로컬 변수에 저장한다.
- 각 서블릿은 마치 자신이 독자적인 시스템에서 동작하는 양 요청을 처리한다.

## 라이브러리를 이해하라

### Java.util.concurrent 패키지를 익혀라

- Thread Safe한 컬렉션을 사용한다.
  ConcurrentHashMap, AtomicLong
- 서로 무관한 작업을 수행할 때는 executor 프레임워크를 사용한다.
- 가능하다면 Thread가 BIocking되지 않는 방법을 사용한다

## 동기화하는 메서드 사이에 존재하는 의존성을 이해하라

### 공유 객체 하나에는 메서드 하나만 사용하라

- 클라이언트에서 잠금 - 클라이언트에서 첫 번째 에서드를 호출하기 전에 서버를 잠근다.
  마지막 메서드를 호출할 때까지 잠금을 유지한다.
- 서버에서 장금 - 서버에다 “서버를 장그고 모든 에서드를 호출한 후 장금을 해제하는” 에서드를 구현한다.
  클라이언트는 이 메서드를 호출하기만 하면 된다.
- 연결(Adapter) 서버 - 잠금을 수행하는 중간 단계를 생성한다.
  '서버에서 잠금' 방식과 유사하지만 원래 서버는 변경하지 않는다.

## 문제상황 : 공유 자원 접근

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/3df2f7cb-65e3-48cb-933b-13edcf8c3c2f/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/fbb5b106-ae92-4842-bdae-188153805e6e/Untitled.png)

- Thread1이 hasNext() true를 받았으나 잠시 다른 일에 선점당한다.
- Thread2가 hasNext0 true를 받고 next()를 호출한다.
- Thread1이 실행을 재개하여 next()를 또 호출한다.

## Client Based Lock

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/6a0278d2-12b9-4b74-bb79-db3651254372/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/1915d5a0-2b06-4317-965f-7a3f7dd76da2/Untitled.png)

- 클라이언트에서 락을 걸어 Thread Safe하게 블록을 보호한다.
- 자원을 사용하는 클라이언트마다 위의 처리를 해줘야 한다. 비효율적!

## Server Based Lock

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/2d069d97-0174-4655-80de-f6aaec189456/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/0e0fa0b1-0cc2-4601-9c9d-c5eadd162f03/Untitled.png)

- 서버에서 두 개로 나눠졌던 동작을 하나로 합쳐 락을 건다.
- 클라이언트에서는 보호된 메서드를 호출하기만 하면 된다.

## Adapter Based Lock

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/37797401-0bef-43bc-b2e1-4261fce2a3d9/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/a3fc4790-6237-469e-805e-101e45249d9d/Untitled.png)

- 어댑터에서 서버에서 두 개로 나눠졌던 동작을 하나로 합쳐 락을 건다.
- 클라이언트에서는 보호된 메서드를 호출하기만 하면 된다.
- 서버의 코드가 외부 코드라서 수정할 수 없을 때 우리 코드에서 Adapter를 만들어 사용한다.

# 동시성 테스트 방법

### **테스트를 했다고 동시성 코드가 100% 올바르다고 증명하기는 불가능하다.
하지만 충분한 테스트는 위험을 낮춘다.**

- 문제를 노출하는 테스트 케이스를 작성하라
- 프로그램의 설정과 시스템 설정과 부하를 바꿔가며 자주 들려라
- 테스트가 실패하면 원인을 추적하라
- 다시 돌렸더니 통과한다는 이유로 그냥 넘어가면 절대로 안된다

## 코드에 보조 코드를 넣어 돌려라

### 드물게 발생하는 오류를 자주 발생시키도록 보조 코드를 추가한다

- 코드에 wait(), sleep(), yield(), priority() 함수를 추가해 직접 구현한다
- 보조코드를 넣어주는 도구를 사용해 테스트한다.
   - 다양한 위치에 ThreadJiggtePoint.jiggle()을 추가해
     무작위로 sleep(), yield()가 호출되도록 한다.
- 테스트 환경에서 보조 코드를 돌려본다.

## 동시성 코드를 실제 환경이나 테스트 환경에서 돌려본다

### 다양한 요청과 상황에 동시성 코드가 정상적으로 동작하는지 확인한다

- 배포하기 전에 테스트 환경에서 충분히 오랜시간 검증한다.
- 동시성 코드를 배포한 후에 모니터링을 통해 문제가 발생하는지 지켜본다.

# 오픈소스 속 동시성 처리

## EIastic Search - LockingAtomicCounter

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/e84285d5-da2e-44fc-a3a5-5d70c43b17ff/Untitled.png)

**AtomicLong**
• Long 자료형을 thread safe하게 램평한 값
• CPU 레벨의 연산인 CAS(compare-and-swap)을 지원해서 thread safe하게 값을 사용할 수 있다.

**ReentrantReadWriteLock**
• 동시에 읽는 스레드는 허용하지만 읽기&쓰기, 쓰기&쓰기의 경우에는 자단한다.
• 읽는 작업이 자주 읽어나고 쓰는 작업이 적을 때 사용하면 좋다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/a9782966-56ae-401a-8f69-997495393be6/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/291d66b8-8bd0-413a-a85e-3ed160b03166/Untitled.png)

- compareAndRun에서 들어온 count 값을 assert를 이용해 validation 했다.
- ReIeasableLock은 elastic search에서 lock* wrapping&H서 만든 것
- 읽기 락을 가지와 counter를 비교하고 runnable을 실행한다.
- increment에서 쓰기 락을 얻어온다.
- counter를 조작할 때는 Thread Safe한 Atomic 변수를 사용했다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/215a6487-4b84-490a-b1d4-d40b77b59fe9/Untitled.png)

1. compareAndRun의 동작을 확인한다.
   counter가 일치하는 경우 runnable이 실행되는 것을 확인한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/7a71d0c1-8b58-4392-9c43-f17f286667e5/Untitled.png)

1. increment의 동작을 확인한다.
   랜덤으로 준 loop의 횟수만큼 increment가 실행되었는지 확인한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/64e59fe2-098b-45af-95c0-9b872cdc3e05/Untitled.png)

1. 다른 Thread에 의해 값이 변했으면 실행되지 않음을 확인한다.
   새로운 쓰레드에서 counter를 increment하고 나서
   현재 쓰레드의 counter가 증가된 상태로 읽어 runnalbe이 실행이 되었는지 확인한다.

**CountDownLatch**
• 현재 쓰레드에서 다른 쓰레드에서 작업이 완료될때까지 기다릴 수 있도록 하는 클래스
• countDown을 통해 count를 마이너스 한다
• count가 0이 될 때까지 await을 통해 기다린다