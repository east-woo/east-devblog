---
title: "[클린코드-13] 코드를 점진적으로 개선하기"
description: "어떻게 글을 작성하고 추가할까요?"
date: 2024-07-09
update: 2024-07-09
tags:
  - 독서
  - Clean Code
series: "Clean Code"
---

**Chapter 13. 동시성**<br>
**page 225 ~ 244**<br>
**부록 407 ~ 466**

# 책의 예제

## 명령형 인수 구문 분석기

### 코드 초안

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/e45a81ba-aa21-4954-ad0f-de6bd6a5bfa7/Untitled.png)

- 모든 로직이 하나의 클래스에 들어가있다.
- 처음부터 지저분한 코드를 짜려는 생각은 없었고, 코드를 어느정도 손봤지만 새로운 인수 유형이 들어오면서 재앙이 시작됐다.
- 이제는 개선해야 할 때라는 걸 깨닫고, **변경 전후 시스템이 동일하게 돌아간다는 사실을 확인하기 위해 테스트들을 작성해뒀다.**
- 자잘하게 **점진적**으로 개선해나갔다.

### 코드 완성본

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/21beaba8-ed19-4b29-93ea-b2d604c75883/Untitled.png)

- Args 클래스에서 코드 중복을 최소화하고, ArgsException 클래스를 분리했다. ArgumentMarshaler클래스를 통해 여러 인수에 대한 추후 확장성을 만들어냈다.
- **코드만 분리해도 설계가 좋아진다.** 관심사를 분리하면 코드를 이
  해하고 보수하기 월썬 더 쉬워진다.

## 책의 예제를 통해 배울 점

- 한번 정도 따라 읽어가며 저자가 '점진적으로' 코드를 개선해나가는 사고 흐름을 따라가면 좋다
- 부담없이 가볍게 읽어본다.
  **자신이 스스로 점진적으로 코드를 개선해보는 경험을 통해 더 많이 배우게 된다**

# 점진적으로 개선하기

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cb148c2e-a3df-4913-bf0f-61861acfbb44/b931381b-9d82-4eb0-9cde-e11e0fd19f06/Untitled.png)

**“프로그램을 망치는 가장 좋은 방법 중 하나는 개선이라는 이름 아래 구조를 크게 뒤집는 행위다”**

# IDE를 활용해 점진적으로 개선하기

## Extract Method : 메서드 추출 기능

코드 블럭을 메서드로 추출할 수 있다

https://gyucheolk.tistory.com/71

## Change Signature : 메소드 파라미터 추가, 삭제 및 변경

메서드의 파라미터 추가하거나 변경할 수 있다.

## Rename : 이름 변경

메서드나 변수 이름을 변경할 수 있다

## Extract Variable : 변수 추출 기능

변수를 추출할 수 있다

Extract FieId : 멤버 변수 추출 기능
특정 값을 멤버 변수로 설정할 수 있다

Extract Constant: 상수 추출 기능
특정 값을 상수로 추출할 수 있다

Pull Members Up & Pull Members Down
하위 클래스의 메서드를 상위로 올리거나 & 상위 클래스의 메서드를 하위로 내릴 수 있다

- MissileStrategy의 attack()을 상위 인터페이스인 AttackStrategy로 올린다.
- @Override도 생기고, 인터페이스에 메서드도 생성된다.