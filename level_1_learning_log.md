# 🏎 Racing Car

## JUnit, AssertJ
### 내용
- 단위 테스트 작성에 대한 학습
- 리팩토링시 빠른 피드백을 얻을 수 있다는 강점을 느낄 수 있었음

## 원시값 포장
### 내용
- 캡슐화를 통해 값의 불변성을 보장
- 객체들의 역할 분담에서 더 장점이 생길 수 있음 (e.g. validation)

## 방어적 복사
### 내용
- 어떤 객체를 참조만 해서 로직을 수행하고자 할 때, 참조할 원본 객체보다 복사된 객체를 이용하는 것이 더 안전
- 복사된 객체가 혹시나 변경 되더라도 원본(원래 객체)에는 영향이 없기 때문
- 불변 객체

## 의존성 주입을 통한 테스트하기 용이한 코드
### 내용
- 의존하고있는 객체를 내부적으로 생성해버리면 의존성이 강해져서 변화에 취약
- 외부에서 의존하는 객체를 주입받는다면 보다 변화에 유연한 설계가 가능
- 전략패턴 (e.g. interface를 활용해서 다양한 자동차 이동전략 생성해서 주입)

---
# 🧧 Lotto

## TDD
### 내용
- Domain부분에 한해서 TDD를 적용하여 개발하는 프로세스를 적용, 연습해 봄
- 실패하는 테스트를 먼저 만들기 -> 통과하는 프로덕션 코드 만들기 -> 리팩토링 -> … 싸이클에 대한 적응

### 링크
- http://cloudrain21.com/test-driven-development
- https://www.guru99.com/test-driven-development.html

## Enum
### 내용
- 로또의 결과를 담당하는 클래스를 Enum으로 작성함
- enum만이 가지고 있는 특성을 이해하고 활용해봄

### 링크
- https://woowabros.github.io/tools/2017/07/10/java-enum-uses.html

## StringBuilder
### 내용
- “+”로 String들을 이어주는 것 보다 StringBuilder를 사용하는 것이 더 성능이 좋음을 이해
- 로또의 요약본을 만들어 줄 때 StringBuilder를 활용해 봄

### 링크
- https://novemberde.github.io/2017/04/15/String_0.html

## Git
### 내용
- 페어 프로그래밍 후 페어의 깃허브 레포지토리에 있는 소스코드를 가져와보는 것에 대해 공부

### 링크
- https://github.com/next-step/nextstep-docs/blob/master/codereview/review-step3.md
- https://readystory.tistory.com/151
- https://www.atlassian.com/git/tutorials/syncing/git-fetch

## 변수에 fianl 키워드 붙여주기
### 내용
- 인스턴스 변수들에 final 키워드를 붙여줘서 성능 최적화가 가능하다는 사실을 이해 (얼리 바인딩)
- 다음에는 메서드 로컬 변수들에도 적용해 보자

## 상속 vs 컴포지션
### 내용
- 상속은 캡술화를 깨뜨린다
- 상속은 상위 클래스의 구현이 변경됨에 따라 하위 클래스의 동작에 이상이 생길 수 있다
- 즉, 변화에 유리하고 캡슐화를 보장해 주는 컴포지션이 더 권장된다
- 하지만, 컴포지션도 단점이 존재한다 (릴레이 함수, 효율성 or 성능 측면 문제 - 사실 고수준 하드웨어 위에서 돌아가는 현대 어플리케이션에서는 큰 문제 x)
- Best Practice: 상속은 정말 확실한 상속 구조로 판단되는 곳에 적용하고, 기본적으로는 컴포지션 사용

---
# 🃏 Blackjack

## Lambda
### 내용
- 간결하게 필요한 메서드를 구현 가능
- 블랙잭의 결과 판별을 enum과 lambda를 활용하여 해결
- 원래 player와 dealer들이 협력해서 결과를 판별하는 구조였음
- outcome enum에 있는 각각의 결과 상수들이 결과 판단을 위한 로직을 스스로 갖고있게 만들어서 본인에게 해당되면 스스로를 반환하도록 구현
- BiPredicate 사용 (두개의 인자를 받고 boolean을 리턴해주는 lambda)

## 다형성의 장점 최대한 활용
### 내용
- player와 dealer를 한번에 처리할 일이 있을 때, participant라는 상위 객체를 활용해서 일괄 처리해주도록 구현
- 반복문, stream 처리 시 유용
- 코드가 훨씬 간결해짐

## Caching
### 내용
- 52장의 카드를 캐싱 해두어서 필요할때마다 매번 생성하지 않고, 캐싱된 카드를 갖다 쓰는 형태로 구현
- 성능 개선 효과

## 커스텀 예외
### 내용
- 커스텀 예외 생성하는 법 학습
- 예외 내부에 메세지를 가지고 있어서 스스로 메세지를 생성
- 추후, 커스텀 예외들을 활용해서 예외에 대한 더 디테일한 처리가 가능할 수 있음

## 추상 메서드의 위치
### 내용
- 가독성을 위해 추상 메서드는 메서드 서열 중 가장 아래로!

---
# ♟ Chess

## Optional
### 내용
- Optional을 활용하여 NPE의 위험을 줄이고 더 깔끔하게 null을 처리할 수 있음
- null 처리 이외에 더 좋은 방법이 있는데도 불구하고 마구잡이로 쓰는 것은 위험함

### 링크
- http://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/

## Web Application (Spark Java)
### 내용
- Web Application이 전체적으로 어떻게 구성되고 동작하는 지 이해
- Web 에서의 MVC 구조 이해
- HTTP Request, Response, Status Code, Method 등등
- Java 에서의 DB 운용 (JDBC)
- Handlebars 문법

### 링크
- https://sparkjava.com/documentation

## DTO
### 내용
- Request, Response를 주고 받을 때 DTO를 통해 주고받는 개념을 학습 및 적용
- dto를 사용하지 않는다면 엔티티에 프레젠테이션을 위한 로직이 추가되면서 엔티티가 복잡해진다.
- dto를 사용하지 않는다면 API 스펙과 엔티티 사이에 의존성이 생긴다. (변화에 유연하지 못하게 됨)
- dto를 사용하지 않는다면 엔티티를 변경함에 따라 발생하는 사이드 이펙트의 추적이 힘들다.

---
# 📝 Todo List

## HTML
### 내용
- html 태그 종류
- dom 구조 (Hierarchy)

## CSS
### 내용
- CSS에 대한 기초적인 지식
- html 요소들을 예쁘게 꾸며주는 역할

## JavaScript
### 내용
- querySelector를 통한 html 요소 추출
- Event Listener를 통한 이벤트 처리
