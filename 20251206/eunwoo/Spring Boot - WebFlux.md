# Spring Boot - WebFlux

---

## 스프링 부트 - 웹플럭스 개요

WebFlux는 스프링 프레임워크의 일부로, 반응형 웹 애플리케이션을 구축하기 위한 프레임워크. 이는 Project Reactor 라이브러리 기반으로 하는 반응형 프로그래밍(Reactive Programming) 프레임워크로, 비동기(Asynchronous) 처리 및 비차단(Non-Blocking) 처리를 지원한다.

## WebFlux의 목표

WebFlux의 핵심 목표는 비동기 및 이벤트 기반 프로그래밍을 통해 높은 확장성과 성능을 제공하는 것이다. 이는 대량의 동시 연결 및 높은 부하에도 효과적으로 대응할 수 있도록 돕는데 중점이 있다.

## WebFlux의 특징

### 반응형 프로그래밍(Reactive Programming)

이벤트 중심의 프로그래밍 스타일이며, 스레드(Thread) 대신 데이터 스트림에 초점을 맞춘다. 대량의 동시요청을 처리하는 애플리케이션의 확장성과 응답성을 향상시키는데 매우 효과적이다. 적은 리소스로 많은 동시 연결을 처리할 수 있다. WebFlux는 Project Reactor와 같은 라이브러리를 사용하여 데이터를 비동기적으로 처리하기 위한 반응형 유형인 Mono와 Flux를 제공한다.

> 반응형 프로그래밍이란?
반응형 프로그래밍(Reactive Programming)은 프로그래밍 패러다임 중 하나로, 데이터 흐름과 변화에 중점을 두는 접근방식이다. 이 패러다임은 시간과 상태의 변화에 반응하는 애플리케이션을 쉽게 작성하고 관리할 수 있도록 초점을 맞춘다.
> 

### 비동기 및 비차단(Asynchronous and Non-Blocking)

Spring WebFlux는 Asynchronous 및 Non-Blocking I/O 방식을 사용하여 적은 수의 스레드로 많은 수의 동시 요청을 처리할 수 있다. 이는 Synchronous 및 Blocking을 사용하는 기존 서블릿(Servlet)기반 Spring Web MVC 방식과 달리, 높은 처리량을 제공한다.

### 함수형 프로그래밍(Functional Programming)

반응형 프로그래밍은 불변 데이터와 순수 함수를 사용하는 함수형 프로그래밍 스타일을 지향한다. WebFlux는 함수형 프로그래밍 스타일을 지원하며, 람다(Lambda)와 스트림(Stream)과 같은 기능적 추상화를 제공하여 반응형 코드를 보다 간결하고 읽기 쉽게 작성할 수 있도록 도와준다.

### 스프링 부트와의 통합(Integration with Spring Boot)

WebFlux는 Spring Boot 생태계와 완전히 통합되어 있어 강력한 자동 설정 기능을 제공한다. 이를 통해 개발 프로세스를 간소화하고, 보일러플레이트 코드(Boilerplate Code)를 줄일 수 있다.

> 보일러플레이트 코드(Boilerplate Code)란?
최소한의 변경으로 여러곳에서 재사용되며, 반복적으로 비슷한 형태를 띄는 코드를 의미한다. 다시말하면 반복되는 작업이나 패턴에 대한 일종의 표준화된 코드이다.
> 

## WebFlux의 장단점

### 장점

1. 반응형 프로그래밍의 패러디임을 따르기 때문에, Non-Blocking I/O 작업을 수행할 때 높은 성능과 효율성을 제공한다.
2. 적은 양의 스레드와 최소한의 하드웨어 자원(Resource)으로 효율적인 동시성 핸들링이 가능해진다.
3. 동시성을 극한으로 이용해서 응답속도를 단축할 수 있다.
4. 효율적인 자원 사용이 가능해진다.
5. 요청이 순간적으로 늘어나도 유연하게 커버가 가능하다.

### 단점

1. 기존 Spring MVC와 다른 패러다임을 따르기 때문에 학습 곡선이 높을 수 있다.
2. 비동기 처리 방식을 사용하기 때문에, 전통적인 동기 방식에 익숙한 개발자들은 처음에 적응하기 어려울 수 있다.
3. 오류 처리가 다소 복잡하다.
4. 반응형 프로그래밍을 적용하기 위해서는 기존의 개발 방식과는 다른 사고방식이 필요하다.
5. 디버깅이 어렵다.

## WebFlux 요청 처리 흐름

![webflux-flow.png](webflux-flow.png)

위 그림은 Spring WebFlux의 클라이언트 요청 및 응답 처리 흐름을 표현한 것이다.

1. Client의 요청
2. Netty 등의 서버 엔진을 거쳐 HttpHandler가 들어오는 요청을 전달받는다. ServerWebExchange를 생성한 후 WebFilter 체인으로 전달한다.
3. ServerWebExchage는 WebFilter 체인에서 전처리 과정을 거친 후 WebHandler 인터페이스의 구현체인 DispatcherHandler에게 전달한다.
4. DispatcherHandler에서는 HandlerMapping List를 원본 Flux의 소스로 전달한다.
5. ServerWebExchange를 처리할 핸들러를 조회한다.
6. 조회된 핸들러의 호출을 HandlerAdapter에게 위임한다.
7. HandlerAdapter는 ServerWebExchange를 처리할 핸들러를 호출한다.
8. Controller 또는 handlerFunction 형태의 핸들러에서 요청을 처리한다.
9. 요청 처리가 완료된 데이터를 모델에 담아 return 한다
10. return 받은 데이터를 처리할 HandlerResultHandler를 조회한다.
11. 조회된 HandlerResultHandler가 return 받은 데이터를 적절하게 처리한 후 response를 return 한다.

> 참고
편의상 핸들러에서 response 데이터를 return한다는 표현을 사용했지만, 실제 핸들러에서 return되는 것은 response 데이터를 포함하고 이쓴 Flux 또는 Mono Sequence이기 때무에 메서드 호출을 통해 return 된 Reactor Sequence가 즉시 어떤 작업을 수행한다는 의미는 아니다.
> 

## 정리

WebFlux는 확장가능하고, Non-Blocking 방식의 웹 애플리케이션을 구축하는데 있어 강력하고 유연한 반응형 웹 프레임워크이다. 이는 높은 동시성과 낮은 지연 시간 응답이 요구되는 최신 웹 애플리케이션 및 마이크로서비스 아키텍처에 특히 적합하다.

## 참고문서

- [https://docs.spring.io/spring-framework/reference/web/webflux.html](https://docs.spring.io/spring-framework/reference/web/webflux.html)
- [https://m.blog.naver.com/seek316/223311717538](https://m.blog.naver.com/seek316/223311717538)
- [https://techblog.woowahan.com/12903/](https://techblog.woowahan.com/12903/)
- [https://medium.com/@bubu.tripathy/spring-webflux-101-d8a75089b2fd](https://medium.com/@bubu.tripathy/spring-webflux-101-d8a75089b2fd)
