# 20260307

## Flux

### 기본구조

```java
import com.itvillage.utils.Logger;
import reactor.core.publisher.Flux;

/**
 * Flux 기본 예제
 */
public class FluxExample01 {
    public static void main(String[] args) {
        Flux.just(6, 9, 13)
                .map(num -> num % 2)
                .subscribe(remainder -> Logger.info("# remainder: {}", remainder));
    }
}
```

입력으로 들어가는 데이터들을 데이터 소스라고 한다.

오퍼레이터로 map을 사용했다.

다운스트림이 섭스크라이버가 된다. 오퍼레이터로 실행된 값을 실행한다.

### 오퍼레이터 체인구조

```java
import com.itvillage.utils.Logger;
import reactor.core.publisher.Flux;

/**
 * Flux 에서의 Operator 체인 사용 예제
 */
public class FluxExample02 {
    public static void main(String[] args) {
        Flux.fromArray(new Integer[]{3, 6, 7, 9})
                .filter(num -> num > 6)
                .map(num -> num * 2)
                .subscribe(multiply -> Logger.info("# multiply: {}", multiply));
    }
}
```

데이터 소스가 배열로 전달된다. `fromArray`를 이용해서 다운스트림쪽으로 에밋을 한다.

filter를 통해서 배열의 값들을 일부 필터링을하고 데이터를 map 오퍼레이터 쪽으로 전달한다.

map오퍼레이터에서 데이터가공을 수행하고 subscribe쪽으로 데이터들을 에밋한다.

### Mono 2개를 이용하여 Flux 변환하기

```java
import com.itvillage.utils.Logger;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

/**
 * 2개의 Mono를 연결해서 Flux로 변환하는 예제
 */
public class FluxExample03 {
    public static void main(String[] args) {
        Flux<Object> flux =
                Mono.justOrEmpty(null)
                        .concatWith(Mono.justOrEmpty("Jobs"));
        flux.subscribe(data -> Logger.info("# result: {}", data));
    }
}
```

`just` 오퍼레이터는 null값을 포함할 수 없지만 `justOrEmpty` 의 경우는 null값을 포함할 수 있다.

concatWith 를 통해서 데이터를 합쳐 Flux로 변환해준다.

하나는 null이고 하나는 job이므로 job만 출력된다.

### 여러개의 Flux를 이용하여 하나의 Flux 만들기

```java
import com.itvillage.utils.Logger;
import reactor.core.publisher.Flux;

/**
 * 여러개의 Flux를 연결해서 하나의 Flux로 결합하는 예제
 */
public class FluxExample04 {
    public static void main(String[] args) {
        Flux.concat(
                Flux.just("Venus"),
                Flux.just("Earth"),
                Flux.just("Mars"))
            .collectList()
            .subscribe(planetList -> Logger.info("# Solar System: {}", planetList));
    }
}
```

collectList 오퍼레이터는 하나의 리스트로 데이터를 묶어버린다.

그래서 각각 3줄이 출력되는 것이 아닌

하나의 리스트로 3개의 데이터가 들어있는 것으로 출력된다.



## Cold Sequence와 Hot Sequence

### Cold Sequence

Cold Publisher는 Subscriber가 구독할때마다 타임라인의 처음부터 emit된 모든 데이터를 받을 수 있다.

구독 시점이 달라도 새로운 스트림이 생기면서 모든 데이터를 내보내게된다.

구독자(Subscriber)가 구독할 때마다 퍼블리셔(Publisher)가 데이터 생성 과정을 처음부터 다시 시작하는 데이터 흐름 방식

### Hot Sequence

Hot Publisher는 첫번째 Subscriber가 처음부터 데이터를 다 받지만 두번째 Subscriber는 구독한 시점부터(중간부터) 데이터를 받게된다.

구독 시점이 달라도 하나의 스트림으로 데이터를 내보내게되므로 구독 시점에 따라 받는 데이터 양이 달라진다.
