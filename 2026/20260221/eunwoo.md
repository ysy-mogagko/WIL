# 20260207

## Spring web Flex 

## Flux의 구성요소 분석해보기

- 참고용 로깅 util
    
    ```java
    import lombok.extern.slf4j.Slf4j;
    
    @Slf4j
    public class Logger {
        public static void info(Object data) {
            log.info("{}",data);
        }
    
        public static void info(String msg, Object data) {
            log.info(msg, data);
        }
    
        public static void doOnNext(Object data) {
            log.info("# doOnNext(): {}", data);
        }
    
        public static void doOnNext(String operator, Object data) {
            log.info("# doOnNext() {}: {}", operator, data);
        }
    
        public static void doOnNext(String taskName, String operator, Object data) {
            log.info("# doOnNext() {} {}: {}", taskName, operator, data);
        }
    
        public static void doOnRequest(Object data) {
            log.info("# doOnRequest(): {}", data);
        }
    
        public static void onNext(Object data) {
            log.info("# onNext(): {}", data);
        }
    
        public static void onNext(Object data1, Object data2) {
            log.info("# onNext(): {} : {}", data1, data2);
        }
    
        public static void onError(Throwable error) {
            log.error("error happened: ", error);
        }
    
        public static void onComplete() {
            log.error("# onComplete()");
        }
    
        public static void onComplete(Object data) {
            log.info("# onComplete(): {}", data);
        }
    
        public static void onNext(String message, Object data) {
            log.info("# {} onNext(): {}", message, data);
        }
    
        public static void filter(Object data) {
            log.info("# filter(): {}", data);
        }
        public static void map(Object data) {
            log.info("# map(): {}", data);
        }
    }
    
    ```
    

```java
import com.reactive.demo.util.Logger;
import reactor.core.publisher.Flux;

public class HelloReactor {
    public static void main (String[] args) {
        Flux<String> sequence = Flux.just("Hello", "Reactor");
        sequence
            .map(data -> data.toLowerCase())
            .subscribe(data -> Logger.onNext(data));
    }
}
```

Flux 부분은 퍼블리셔

just 와 map은 오퍼레이터 (연산자들)

오퍼레이터들을 체인형태로 선언하고 있는데 이 형태를 시퀀스라고 한다.

리액티브 프로그래밍의 3가지 큰 흐름

1. 퍼블리셔가 데이터 생성
2. 오퍼레이터를 통한 가공
3. 가공한 데이터를 subscribe측에 전달
