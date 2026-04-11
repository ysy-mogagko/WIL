# 20260411 eunwoo

## Spring Reactive

### checkpoint() Operator를 사용하는 방법

- 특정 Operator 체인 내에서만 assembly stacktrace를 캡쳐한다
- checkpoint(description)를 사용하면 에러 발생 시, checkpoint(description)를 추가한 지점의 assembly stacktrace를 생략하고 description을 통해 에러 발생 지점을 예상할 수 있다.
- checkpoint(description, true) = checkpoint() + checkpoint(”description”)
    - 에러 발생 시, 고유한 식별자 등의 description과 assembly stack trace(traceback)를 모두 출력한다.

### log() operator 사용

- Flux 또는 Mono에서 발생하는 signal event를 출력해준다(onNext, onError, onComplete, subscriptions, cancellations, requests)
- 여러개의 log()를 사용할 수 있으며, Operator마다 전파되는 signal event를 확인할 수 있다.
- Custom Category를 입력해서 Operator마다 출력되는 signal event를 구분할 수 있다.
- 에러 발생 시, stacktrace도 출력해준다.
- debug mode라면 traceback도 출력해준다.
