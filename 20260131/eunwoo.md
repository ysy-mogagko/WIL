# 20260131

## Spring web Flex 

Kevin의 알기 쉬운 Spring Reactive Web Applications: Reactor 강의 수강 
- https://inf.run/wRn4X

# 1. 리액티브 시스템과 리액티브 프로그래밍의 의미

## 리액티브 시스템 이란?

클라이언트의 요청에 머뭇거리지 않고 반응을 잘하는것.

### 리액티브 선언문

https://www.reactivemanifesto.org

-> 리액티브 시스템의 설계원칙 4가지

1. Responsive 응답성 : 시스템이 가능한한 즉각적으로 반응
2. Resilient 회복성 : 장애에 직면했을때 응답성을 유지
3. Elastic 탄력성 : 작업량이 다양하게 변화한다고 해도 탄력성이 유지된다.
4. Message Driven : 느슨한 결합과 경계성 위치투명성을 보장한다.

### 리액티브 시스템의 특징

- 높은 응답성
- 탄력적이고 유연하다
- 메시지 기반으로 동작한다.

리액티브 시스템을 구축하는데 필요한 프로그래밍 모델 : https://en.wikipedia.org/wiki/Reactive_programming

### 리액티브 프로그래밍의 특징

- 데이터 소스에 변경이 있을때마다 데이터를 전파
- 선언형 프로그래밍 패러다임 (실행할 동작을 구체적으로 명시하지 않고 목표만 정의함 - 명령형 프로그래밍과 다름!!!)
- 함수형 프로그래밍 기법 사용

### 명령형 프로그래밍 vs 선언형 프로그래밍

익숙하지 않은 선언형 프로그래밍 방식으로 개발하려면 적응이 필요

### 리액티브 스트림즈(Reactive Streams)란?

리액티브 프로그래밍을 표준화 한 명세

4가지 interface를 정의한다.

- publisher - 데이터를 통제하는 주체
- subscriber - publisher에서 통제한 데이터를 구독하는 구독자
- subscription - 구독 자체를 정의한 인터페이스
- processor - 퍼블리셔와 섭스크라이버를 동시에 사용가능

### Reactive Streams를 구현한 구현체

- RxJava
- Java Flow API(Java 9 버전부터 지원)
- Akka Streams
- Reactor → 앞으로 학습할것
- 등등…
