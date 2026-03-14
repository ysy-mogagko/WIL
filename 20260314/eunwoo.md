## 서론

Backpressure는 퍼블리셔로부터 섭스크라이버에게 끊임없이 전달하는 데이터를 안정적으로 처리하게 하기 위한 데이터 처리방식

퍼블리셔와 섭스크라이버 사이에 데이터를 어떻게 전달하는지 이해하면 좋다.

## Publisher 와 Subscriber간의 프로세스

1. subscriber에서 subscribe()를 호출하여 구독을 수행한다
2. publisher에서 send onSubscribe signal로 구독이 정상 시작한다고 전달한다.
3. subscriber에서 request signal of N → 데이터를 전달받기 위해 request를 보낸다.
4. publisher에서 onNext 시그널을 보내면서 데이터를 전달한다.
5. subscriber에서 받은 데이터 처리가 끝나면 다시 request signal을 보내서 데이터를 요청한다.
6. publisher 에서 onNext 시그널을 보내면서 데이터를 전달한다.
7. request signal 과 onNext 시그널을 반복해서 보낸다.
8. 만약 publisher에서 request signal을 받고 이게 마지막 데이터일 경우엔 onComplete signal을 보내고 프로세스를 종료한다.
9. 만약 데이터를 emit하는 과정에서 에러가 발생할경우 onError 를 전달하고 프로세스를 종료한다.

## Backpressure란?

### 예시

유명 가수의 콘서트 장에서 출입구가 2개가 있고 많은 관객들이 콘서트장으로 들어가야한다고 생각해보자.

한 출입구에는 많은 사람들이 무작위로 막 밀고 들어간다고 해보자. 그러면 안전사고가 발생할 수 도 있고 위험하다.

다른 출입구는 차례차례 들어간다고 해보자.
그러면 시간은 걸리겠지만 티켓확인도 철저하게 되고 안전하게 들어갈 수 있다.

### Backpressure란?

들어오는 데이터를 적절하게 제어해서 과부하가 오지 않게 조절하는 수단이다.

Publisher에서 emit 되는 데이터를 Subscriber쪽에서 안정적으로 처리하기 위한 제어 기능.

만약 Backpressure가 없는 상황이라면..

Publisher의 데이터 전송 속도는 빠른데 Subscriber의 처리속도는 느리다.

이렇게 될 경우 최악의 경우는 데이터가 쌓이고 쌓여 시스템이 다운될 수 있다.

그러므로 리엑티브에선 backpressure 기능이 반드시 필요하다.

## Reactor에서의 Backpressure 처리 방법

- 요청 데이터의 개수를 제어하는 방법
    - Subscriber가 적절히 처리할 수 있는 수준의 데이터 개수를 Publisher에게 요청
- Backpressure 전략을 사용하는 방법
    - Reactor에서 제공하는 Backpressure 전략을 사용

## Backpressure 전략

1. IGNORE 전략
    1. Backpressure를 적용하지 않는다.
2. ERROR 전략
    1. Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, Exception을 발생하는 전략
3. DROP 전략
    1. Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 먼저 emit 된 데이터부터 Drop 시키는 전략
4. LATEST 전략
    1. Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 가장 최근(나중)에 emit된 데이터부터 버퍼에 채우는 전략
5. BUFFER 전략
    1. Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 안에 있는 데이터를 Drop 시키는 전략
