# 20260321 eunwoo

## Backpressure

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


## Subscriber가 처리 가능한 만큼의 request 갯수를 조절하는 Backpressure 예제

### Example 01

```java
import com.reactive.demo.util.Logger;
import com.reactive.demo.util.TimeUtils;
import org.reactivestreams.Subscription;
import reactor.core.publisher.BaseSubscriber;
import reactor.core.publisher.Flux;

/**
 * Subscriber가 처리 가능한 만큼의 request 개수를 조절하는 Backpressure 예제
 */
public class BackpressureExample01 {
    public static void main(String[] args) {
        Flux.range(1, 5)
            .doOnNext(Logger::doOnNext)
            .doOnRequest(Logger::doOnRequest)
            .subscribe(new BaseSubscriber<Integer>() {
                @Override
                protected void hookOnSubscribe(Subscription subscription) {
                    request(1);
                }

                @Override
                protected void hookOnNext(Integer value) {
                    TimeUtils.sleep(2000L);
                    Logger.onNext(value);
                    request(1);
                }
            });
    }
}
```

구독 시점에서 hookOnSubscriber 라는 메서드를 이용해서 데이터 요청 개수를 1로 지정을 한다.

퍼블리셔 쪽에서 데이를 에밋하면 hookOnNext를 이용해서 에밋된 데이터를 처리하는데 2초정도로 처리 시간에 제한을 걸어버린다.

오퍼레이터 설명으로 

doOnNext 는 업스트림에서 에밋한 것을 로그로 출력을 하고 있다.

doOnRequest는 섭스크라이버 쪽에서 요청한 개수를 출력하고있다.

- 실행 완료 로그
    
    ```bash
    오전 11:29:22: 실행 중 ':com.reactive.demo.section6.BackpressureExample01.main()'…
    
    Starting Gradle Daemon...
    Gradle Daemon started in 1 s 790 ms
    > Task :compileJava
    > Task :processResources UP-TO-DATE
    > Task :classes
    
    > Task :com.reactive.demo.section6.BackpressureExample01.main()
    11:29:41.324 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 1
    11:29:41.327 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 1
    11:29:43.332 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 1
    11:29:43.332 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 1
    11:29:43.333 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 2
    11:29:45.333 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 2
    11:29:45.334 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 1
    11:29:45.334 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 3
    11:29:47.336 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 3
    11:29:47.336 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 1
    11:29:47.336 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 4
    11:29:49.341 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 4
    11:29:49.341 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 1
    11:29:49.341 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 5
    11:29:51.341 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 5
    11:29:51.341 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 1
    
    BUILD SUCCESSFUL in 27s
    3 actionable tasks: 2 executed, 1 up-to-date
    Consider enabling configuration cache to speed up this build: https://docs.gradle.org/9.3.0/userguide/configuration_cache_enabling.html
    오전 11:29:51: 실행이 완료되었습니다 ':com.reactive.demo.section6.BackpressureExample01.main()'.
    
    ```
    

### Example 02

```java

import com.reactive.demo.util.Logger;
import com.reactive.demo.util.TimeUtils;
import org.reactivestreams.Subscription;
import reactor.core.publisher.BaseSubscriber;
import reactor.core.publisher.Flux;

/**
 * Subscriber가 처리 가능한 만큼의 request 갯수를 조절하는 Backpressure 예제
 */
public class BackpressureExample02 {
    public static int count = 0;
    public static void main(String[] args) throws InterruptedException {
        Flux.range(1, 5)
            .doOnNext(Logger::doOnNext)
            .doOnRequest(Logger::doOnRequest)
            .subscribe(new BaseSubscriber<Integer>() {
                @Override
                protected void hookOnSubscribe(Subscription subscription) {
                    request(2);
                }

                @Override
                protected void hookOnNext(Integer value) {
                    count++;
                    Logger.onNext(value);
                    if (count == 2) {
                        TimeUtils.sleep(2000L);
                        request(2);
                        count = 0;
                    }
                }
            });
    }
}

```

두개씩 전달 받고 하나씩 처리하는 예제

- 실행 완료 로그
    
```bash
오전 11:31:07: 실행 중 ':com.reactive.demo.section6.BackpressureExample02.main()'…

> Task :compileJava
> Task :processResources UP-TO-DATE
> Task :classes

> Task :com.reactive.demo.section6.BackpressureExample02.main()
11:31:09.711 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 2
11:31:09.715 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 1
11:31:09.715 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 1
11:31:09.715 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 2
11:31:09.715 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 2
11:31:11.717 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 2
11:31:11.718 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 3
11:31:11.718 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 3
11:31:11.718 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 4
11:31:11.718 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 4
11:31:13.721 [main] INFO com.reactive.demo.util.Logger -- # doOnRequest(): 2
11:31:13.721 [main] INFO com.reactive.demo.util.Logger -- # doOnNext(): 5
11:31:13.721 [main] INFO com.reactive.demo.util.Logger -- # onNext(): 5

BUILD SUCCESSFUL in 6s
3 actionable tasks: 2 executed, 1 up-to-date
Consider enabling configuration cache to speed up this build: https://docs.gradle.org/9.3.0/userguide/configuration_cache_enabling.html
오전 11:31:13: 실행이 완료되었습니다 ':com.reactive.demo.section6.BackpressureExample02.main()'.

```
    

## 전략

### 에러 전략

```java

import com.reactive.demo.util.Logger;
import com.reactive.demo.util.TimeUtils;
import reactor.core.publisher.Flux;
import reactor.core.scheduler.Schedulers;

import java.time.Duration;

/**
 * Unbounded request 일 경우, Downstream 에 Backpressure Error 전략을 적용하는 예제
 *  - Downstream 으로 전달 할 데이터가 버퍼에 가득 찰 경우, Exception을 발생 시키는 전략
 */
public class BackpressureStrategyErrorExample {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(1L))
            .onBackpressureError()
            .doOnNext(Logger::doOnNext)
            .publishOn(Schedulers.parallel())
            .subscribe(data -> {
                    TimeUtils.sleep(5L);
                    Logger.onNext(data);
                },
                error -> Logger.onError(error));

        TimeUtils.sleep(2000L);
    }
}

```

Schedulers 를 이용해 스레드 하나를 더 추가했다. (나중에 설명하는 강의있음)

숫자 0부터 하지만 처리 속도가 느려서 버퍼가 가득 찬 시점에 에러를 발생시킨다.


### 드랍 전략

```java

import com.reactive.demo.util.Logger;
import com.reactive.demo.util.TimeUtils;
import reactor.core.publisher.Flux;
import reactor.core.scheduler.Schedulers;

import java.time.Duration;

/**
 * Unbounded request 일 경우, Downstream 에 Backpressure Drop 전략을 적용하는 예제
 *  - Downstream 으로 전달 할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 먼저 emit 된 데이터를 Drop 시키는 전략
 */
public class BackpressureStrategyDropExample {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(1L))
            .onBackpressureDrop(dropped -> Logger.info("# dropped: {}", dropped))
            .publishOn(Schedulers.parallel())
            .subscribe(data -> {
                    TimeUtils.sleep(5L);
                    Logger.onNext(data);
                },
                error -> Logger.onError(error));

        TimeUtils.sleep(2000L);
    }
}
```

버퍼가 가득 찬 시점에 에밋된 데이터는 드랍한다.


### Latest 전략

```java

import com.reactive.demo.util.Logger;
import com.reactive.demo.util.TimeUtils;
import reactor.core.publisher.Flux;
import reactor.core.scheduler.Schedulers;

import java.time.Duration;

/**
 * Unbounded request 일 경우, Downstream 에 Backpressure Latest 전략을 적용하는 예제
 *  - Downstream 으로 전달 할 데이터가 버퍼에 가득 찰 경우,
 *    버퍼 밖에서 폐기되지 않고 대기하는 가장 나중에(최근에) emit 된 데이터부터 버퍼에 채우는 전략
 */
public class BackpressureStrategyLatestExample {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(1L))
            .onBackpressureLatest()
            .publishOn(Schedulers.parallel())
            .subscribe(data -> {
                    TimeUtils.sleep(5L);
                    Logger.onNext(data);
                },
                error -> Logger.onError(error));

        TimeUtils.sleep(2000L);
    }
}

```

drop 방식과 비슷하다. 버퍼가 가득 찼을때 버퍼쪽으로 들어오는 데이터 들은 폐기된다.

차이점은 drop방식의 경우 버퍼가 가득 찬 상태에서 에밋되는 데이터는 그 즉시 drop이 된다. 그런데 latest 의 경우는 즉시 drop되지 않고 데이터가 하나 더 들어오게 된다. 그러면 들어가지 못한 이전 데이터는 drop 된다. 즉 최신데이터 하나만 계속 남게된다. 


### Buffer 전략  - Latest Drop

```java
import reactor.core.publisher.BufferOverflowStrategy;
import reactor.core.publisher.Flux;
import reactor.core.scheduler.Schedulers;

import java.time.Duration;

/**
 * Unbounded request 일 경우, Downstream 에 Backpressure Buffer DROP_LATEST 전략을 적용하는 예제
 *  - Downstream 으로 전달 할 데이터가 버퍼에 가득 찰 경우,
 *    버퍼 안에 있는 데이터 중에서 가장 최근에(나중에) 버퍼로 들어온 데이터부터 Drop 시키는 전략
 */
public class BackpressureStrategyBufferDropLatestExample {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(300L))
            .doOnNext(data -> Logger.info("# emitted by original Flux: {}", data))
            .onBackpressureBuffer(2,
                    dropped -> Logger.info("# Overflow & dropped: {}", dropped),
                    BufferOverflowStrategy.DROP_LATEST)
            .doOnNext(data -> Logger.info("# emitted by Buffer: {}", data))
            .publishOn(Schedulers.parallel(), false, 1)
            .subscribe(data -> {
                    TimeUtils.sleep(1000L);
                    Logger.onNext(data);
                },
                error -> Logger.onError(error));

        TimeUtils.sleep(3000L);
    }
}

```

버퍼 전략은 버퍼 밖에 데이터가 없고 무조건 버퍼 안에 데이터만 남게된다.

이때 새로운 데이터가 들어오면 버퍼 안의 자리가 안비어있다면 버리고 비어있다면 데이터를 넣는다.

- 실행결과
    
```bash
오전 11:56:40: 실행 중 ':com.reactive.demo.section6.BackpressureStrategyBufferDropLatestExample.main()'…

> Task :compileJava
> Task :processResources UP-TO-DATE
> Task :classes

> Task :com.reactive.demo.section6.BackpressureStrategyBufferDropLatestExample.main()
11:56:42.661 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 0
11:56:42.665 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by Buffer: 0
11:56:42.958 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 1
11:56:43.258 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 2
11:56:43.558 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 3
11:56:43.559 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 3
11:56:43.666 [parallel-1] INFO com.reactive.demo.util.Logger -- # onNext(): 0
11:56:43.667 [parallel-1] INFO com.reactive.demo.util.Logger -- # emitted by Buffer: 1
11:56:43.857 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 4
11:56:44.157 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 5
11:56:44.157 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 5
11:56:44.457 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 6
11:56:44.457 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 6
11:56:44.668 [parallel-1] INFO com.reactive.demo.util.Logger -- # onNext(): 1
11:56:44.668 [parallel-1] INFO com.reactive.demo.util.Logger -- # emitted by Buffer: 2
11:56:44.757 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 7
11:56:45.057 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 8
11:56:45.058 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 8
11:56:45.357 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 9
11:56:45.357 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 9

BUILD SUCCESSFUL in 4s
3 actionable tasks: 2 executed, 1 up-to-date
Consider enabling configuration cache to speed up this build: https://docs.gradle.org/9.3.0/userguide/configuration_cache_enabling.html
오전 11:56:45: 실행이 완료되었습니다 ':com.reactive.demo.section6.BackpressureStrategyBufferDropLatestExample.main()'.

```
    

### Buffer 전략  - Oldest Drop

```java

import com.reactive.demo.util.Logger;
import com.reactive.demo.util.TimeUtils;
import reactor.core.publisher.BufferOverflowStrategy;
import reactor.core.publisher.Flux;
import reactor.core.scheduler.Schedulers;

import java.time.Duration;

/**
 * Unbounded request 일 경우, Downstream 에 Backpressure Buffer DROP_OLDEST 전략을 적용하는 예제
 *  - Downstream 으로 전달 할 데이터가 버퍼에 가득 찰 경우,
 *    버퍼 안에 있는 데이터 중에서 가장 먼저 버퍼로 들어온 오래된 데이터부터 Drop 시키는 전략
 */
public class BackpressureStrategyBufferDropOldestExample {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(300L))
            .doOnNext(data -> Logger.info("# emitted by original Flux: {}", data))
            .onBackpressureBuffer(2,
                dropped -> Logger.info("# Overflow & dropped: {}", dropped),
                BufferOverflowStrategy.DROP_OLDEST)
            .doOnNext(data -> Logger.info("# emitted by Buffer: {}", data))
            .publishOn(Schedulers.parallel(), false, 1)
            .subscribe(data -> {
                    TimeUtils.sleep(1000L);
                    Logger.onNext(data);
                },
                error -> Logger.onError(error));

        TimeUtils.sleep(3000L);
    }
}

```

새로운 데이터가 들어오면 버퍼 안의 자리가 안비어있다면 버퍼 안에 제일 오래된 데이터를 버리고 새로운 데이터를 버퍼로 넣는다

- 실행결과

```bash
오후 12:25:42: 실행 중 ':com.reactive.demo.section6.BackpressureStrategyBufferDropOldestExample.main()'…

> Task :compileJava
> Task :processResources UP-TO-DATE
> Task :classes

> Task :com.reactive.demo.section6.BackpressureStrategyBufferDropOldestExample.main()
12:25:45.084 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 0
12:25:45.088 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by Buffer: 0
12:25:45.383 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 1
12:25:45.682 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 2
12:25:45.983 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 3
12:25:45.985 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 1
12:25:46.088 [parallel-1] INFO com.reactive.demo.util.Logger -- # onNext(): 0
12:25:46.088 [parallel-1] INFO com.reactive.demo.util.Logger -- # emitted by Buffer: 2
12:25:46.283 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 4
12:25:46.582 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 5
12:25:46.582 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 3
12:25:46.882 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 6
12:25:46.882 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 4
12:25:47.090 [parallel-1] INFO com.reactive.demo.util.Logger -- # onNext(): 2
12:25:47.090 [parallel-1] INFO com.reactive.demo.util.Logger -- # emitted by Buffer: 5
12:25:47.182 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 7
12:25:47.483 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 8
12:25:47.483 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 6
12:25:47.783 [parallel-2] INFO com.reactive.demo.util.Logger -- # emitted by original Flux: 9
12:25:47.783 [parallel-2] INFO com.reactive.demo.util.Logger -- # Overflow & dropped: 7

BUILD SUCCESSFUL in 5s
3 actionable tasks: 2 executed, 1 up-to-date
Consider enabling configuration cache to speed up this build: https://docs.gradle.org/9.3.0/userguide/configuration_cache_enabling.html
오후 12:25:47: 실행이 완료되었습니다 ':com.reactive.demo.section6.BackpressureStrategyBufferDropOldestExample.main()'.

```

