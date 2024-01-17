---
title: "Git Commit Message Convention"
description: "어떻게 글을 작성하고 추가할까요?"
date: 2024-01-13
update: 2024-01-13
tags:
  - Convention
series: "Convention"
---

## 1. Git 커밋 메시지란 무엇인가요?
   Git의 맥락에서 커밋 메시지는 특정 커밋의 변경 사항을 설명하는 간략한 설명입니다. 이는 프로젝트 이력에 대한 문서 역할을 하며, 특정 변경이 이루어진 이유에 대한 통찰력을 제공하고 공동 작업자가 시간에 따른 코드베이스의 발전을 더 쉽게 이해하고 추적할 수 있도록 해줍니다.

## 2. Git 커밋 메시지 규칙의 중요성
### 2.1 Git 커밋 메시지 규칙은 무엇인가요?
   Git 커밋 메시지 규칙은 커밋 메시지를 작성하는 일관되고 표준화된 방법을 정의하는 일련의 지침 또는 규칙입니다. 여기에는 변경 내용의 성격에 대한 정보를 전달하기 위한 구조화된 형식과 특정 규칙이 포함됩니다.

### 2.2 Git 커밋 규칙을 따라야 하는 이유
명확성 및 가독성: 일관된 커밋 메시지를 통해 팀 구성원은 각 커밋의 목적과 영향을 더 쉽게 이해할 수 있습니다.
검색 및 탐색: 체계적으로 구성된 커밋 메시지는 버전 기록 검색 기능을 향상시켜 특정 변경 사항을 신속하게 식별하는 데 도움이 됩니다.
자동화 및 도구: 많은 개발 도구 및 플랫폼은 변경 로그 생성 또는 CI/CD 파이프라인 트리거와 같은 자동화된 프로세스에 대한 커밋 메시지 규칙을 활용합니다.

## 3. Git 커밋 메시지 작성 방법
### 3.1 기본 형식(Commit Message 구조)
#### 3.1.1 제목
   제목은 변경 사항을 간략하게 요약한 것입니다. 50자 이내로 작성해야 하며 명령형으로 작성해야 합니다.

예:

sql
Copy code
Add feature: Improve user authentication

#### 3.1.2 텍스트
텍스트는 추가 세부 정보를 제공하고 컨텍스트를 제공하며 변경이 필요한 이유를 설명합니다. 선택사항이지만 복잡한 변경에 권장됩니다.

예:

sql
Copy code
This commit introduces enhancements to the user authentication system, improving security and adding support for multi-factor authentication.
#### 3.1.3 바닥글
바닥글에는 문제에 대한 참조나 주요 변경 사항과 같은 모든 메타데이터가 포함됩니다. 또한 선택 사항입니다.

예:

bash
Copy code
Closes #123
### 3.2 자주 사용되는 태그 유형
feat: 사용자를 위한 새로운 기능입니다.
수정: 버그 수정입니다.
문서: 문서가 변경되었습니다.
스타일: 코드 스타일 변경(서식 등).
리팩터링: 기능을 변경하지 않는 코드 리팩터링입니다.
테스트: 테스트를 추가하거나 수정합니다.
### 3.3 기타 태그 유형
프로젝트 및 규칙에 따라 추가 태그 유형을 정의할 수 있습니다.

### 3.4 Git 커밋 메시지 작성 예시
sql
Copy code
feat: Add payment gateway integration

This commit implements the integration of a new payment gateway to enhance the user checkout experience.

- Integrate XYZ payment gateway SDK
- Update payment processing logic
- Add new payment options to the UI

Closes #456
### 4. Git 커밋 메시지 자동화 방법
   4.1 Git 커밋 메시지 템플릿 설정
   규칙을 준수하도록 커밋 메시지 템플릿을 구성합니다. 이 템플릿에는 커밋 메시지 작성을 위한 사전 정의된 섹션과 지침이 포함될 수 있습니다.

### 4.2 Git 커밋 메시지 자동화 도구
Commitizen 또는 Husky와 같은 도구를 기존 커밋 플러그인과 함께 사용하여 지정된 규칙에 따라 커밋 메시지 생성을 자동화하세요. 이러한 도구는 개발자에게 대화형 프로세스를 안내하여 잘 구조화된 커밋 메시지를 생성할 수 있습니다.

Git 커밋 메시지 규칙을 따르고 자동화를 활용함으로써 팀은 협업을 간소화하고 깨끗한 버전 기록을 유지하며 전체 프로젝트 문서를 향상시킬 수 있습니다.