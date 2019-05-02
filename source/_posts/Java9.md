---
title: JAVA8말고 JAVA9를 사용해볼까
date: 2019-04-23 03:07:13
tags: 
    - java
    - java9
img: /images/java9/java9.jpg
author: 김형진
---

![](/images/java9/java9.jpg)

현재 많은 개발자가 Java8 버전을 사용하여 개발을 하고 있습니다.

Java9 부터는 6개월마다 새로운 버전의 Java가 출시되면서 현재 12버전까지 출시 하였습니다.

그 중에서  JAVA9 버전에 관해 대해 자세히 알아봅니다.



## 마이그레이션시 우려 사항

많은 개발자들이 Java8에서 Java9로 업그레이드 할 때 많은 변경으로 인해 애플리케이션이 중단될수 있다는 걱정이 있습니다. 변경 사항 중 하나는 내부 API의 캡슐화 입니다. 애플리케이션에서 내부 API를 사용하지 않는 경우 Java9 이상으로 마이그레이션 하는것은 어렵지 않습니다.



## 알아두면 좋을 변경된 점



### **110: HTTP 2 Client**

- HttpURLConnection를 대체
  - 요청,응답의 하나의 스레드만 지원
  - 오버헤드 발생
- jdk.incubator.http 패키지 추가
- HTTP/1.1 및 HTTP/2 프로토콜 지원
  - JAVA1.8 까지는 HTTP/ 1.1만을 지원
- 동기/비동기 지원



```java
HttpClient client = HttpClient
    .newBuilder()
    .version(Version.HTTP_2)
    .build();

//동기 호출
HttpResponse<String> response = client.send(
    HttpRequest
        .newBuilder(TEST_URI)
        .POST(BodyProcessor.fromString("Hello world"))
        .build(),
    BodyHandler.asString()
);

//비동기 호출
//임의의 정수가 비동기적으로 요청, 완료 될 때까지 주 스레드 대기
List<CompletableFuture<String>> responseFutures = new Random()
    .ints(10)
    .mapToObj(String::valueOf)
    .map(message -> client
        .sendAsync(
          HttpRequest.newBuilder(TEST_URI)
            .POST(BodyProcessor.fromString(message))
            .build(),
          BodyHandler.asString()
        )
        .thenApply(r -> r.body())
    )
    .collect(Collectors.toList());

CompletableFuture.allOf(responseFutures.toArray(new CompletableFuture<?>[0])).join();

responseFutures.stream().forEach(future -> {
  LOGGER.info("Async response: " + future.getNow(null));
});


Map<HttpRequest, CompletableFuture<HttpResponse<String>>> responses =
  client.sendAsync(
    HttpRequest.newBuilder(TEST_URI)
      .POST(BodyProcessor.fromString(TEST_MESSAGE))
      .build(),
    MultiProcessor.asMap(request -> Optional.of(BodyHandler.asString()))
  ).join();

responses.forEach((request, responseFuture) -> {
  LOGGER.info("Async response: " + responseFuture.getNow(null));
});
```



### 266: More Concurrency Updates (reactive stream)

- CompletableFuture  개선
- reactive stream 도입
  - 게시자, 구독자, 구독, 프로세서
- java.util.concurrent.Flow 패키지 추가



Reactive Streams 표준 구현은 [java.util.concurrent.Flow](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/Flow.html) 클래스에 있으며 `Flow`클래스 내에 정적 인터페이스로 번들로 제공

```java
public final class Flow {
    private Flow() {} // 인스턴스 생성 불가
    
    @FunctionalInterface
    public static interface Publisher<T> {
        public void subscribe(Subscriber<? super T> subscriber);
    }
    
    public static interface Subscriber<T> {
        public void onSubscribe(Subscription subscription);
        public void onNext(T item);
        public void onError(Throwable throwable);
        public void onComplete();
    }
    
    public static interface Subscription {
        public void request(long n);
        public void cancel();
    }
    
    public static interface Processor<T,R> extends Subscriber<T>, Publisher<R> {}
    
}
```

게시자 구현이 포함되어 있는 `SubmissionPublisher`

- `submit(T item)`메서드를 사용하여 구독자에게 푸시 할 항목을 허용하는 단순한 게시자 역할
- `submit`메서드를 실행하면 구독자에게 비동기적으로 푸시

```java
public class PrintSubscriber implements Subscriber<Integer> {
    private Subscription subscription;
    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        subscription.request(1);
    }
    @Override
    public void onNext(Integer item) {
        System.out.println("Received item: " + item);
        subscription.request(1);
    }
    @Override
    public void onError(Throwable error) {
        System.out.println("Error occurred: " + error.getMessage());
    }
    @Override
    public void onComplete() {
        System.out.println("PrintSubscriber is complete");
    }
}
public class SubmissionPublisherExample {
    public static void main(String... args) throws InterruptedException {
        SubmissionPublisher<Integer> publisher = new SubmissionPublisher<>();
        publisher.subscribe(new PrintSubscriber());
        System.out.println("Submitting items...");
        for (int i = 0; i < 10; i++) {
            publisher.submit(i);
        }
        Thread.sleep(1000);
        publisher.close();
    }
}

/*
Submitting items...
Received item: 0
Received item: 1
Received item: 2
Received item: 3
Received item: 4
Received item: 5
Received item: 6
Received item: 7
Received item: 8
Received item: 9
PrintSubscriber is complete
*/
```

구독자 내 `Subscription`에서 `onSubscribe`메소드 에 전달 된 객체를 캡처하여 `Subscription` 나중에 해당 객체와 상호작용 할 수 있습니다.( `subscription.request(1)`호출)

`onNext`메소드 내에서 게시자가 항목 처리를 완료하자마자 다른 항목을 수락 할 준비가 되었음을 게시자에게 알립니다.

 `SubmissionPublisher`와  `PrintSubscriber`를 인스턴스화하고 후자를 전자에 구독한다. 구독이 설정되면 게시자에게 0에서 9까지의 값을 제출하고 구독자에게 값을 비동기 적으로 푸시합니다. 그런 다음 구독자는 값을 표준 출력하여 각 항목을 처리하고 다른 값을 받아 들일 준비가되었음을 구독에 알립니다. 그런 다음 비동기 전송이 완료 될 수 있도록 1 초 동안 주 스레드를 일시 중지합니다. `submit` 메소드가 제출 된 항목을 비동기 적으로 구독자에게 푸시하기 때문에 이는 매우 중요한 단계입니다. 따라서 비동기 작업이 완료 될 때까지 상당한 시간을 제공해야합니다. 마지막으로 게시자를 닫고 구독자에게 가입이 완료되었음을 알립니다.

프로세서를 도입하고 원래의 게시자와 구독자를이 프로세서와 연결할 수 있습니다. 수신 된 값을 10 씩 증가시키고 증가 된 값을 구독자에게 푸시 (push)하는 프로세서를 생성합니다.

각각의 푸시된 값은 10씩 증가하며, 프로세서에 의해 수신된 이벤트(오류 수신 또는 완료 등)는 가입자에게 전달되며, 결과적으로 `PlusTenProcessor`와 `PrintSubscriber` 에 대해 완료된 메시지가 표시된다.

```java
public class PlusTenProcessor extends SubmissionPublisher<Integer> implements Subscriber<Integer> {
    private Subscription subscription;
    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        subscription.request(1);
    }
    @Override
    public void onNext(Integer item) {
        submit(item + 10);
        subscription.request(1);
    }
    @Override
    public void onError(Throwable error) {
        error.printStackTrace();
        closeExceptionally(error);
    }
    @Override
    public void onComplete() {
        System.out.println("PlusTenProcessor completed");
        close();
    }
}
public class SubmissionPublisherExample {
    public static void main(String... args) throws InterruptedException {
        SubmissionPublisher<Integer> publisher = new SubmissionPublisher<>();
        PlusTenProcessor processor = new PlusTenProcessor();
        PrintSubscriber subscriber = new PrintSubscriber();
        publisher.subscribe(processor);
        processor.subscribe(subscriber);
        System.out.println("Submitting items...");
        for (int i = 0; i < 10; i++) {
            publisher.submit(i);
        }
        Thread.sleep(1000);
        publisher.close();
    }
}

/*
Submitting items...
Received item: 10
Received item: 11
Received item: 12
Received item: 13
Received item: 14
Received item: 15
Received item: 16
Received item: 17
Received item: 18
Received item: 19
PlusTenProcessor completed
PrintSubscriber is complete
*/
```



## 269: Convenience Factory Methods for Collections

불변 Collection 메소드가 생겼습니다.

예전에는 아래와 같이 불편하게 사용해야 했습니다.

```java
List<String> band = new ArrayList<>();
band.add("Bruce");
band.add("Steve");
band.add("Adrian");
band.add("Janick");
band.add("Nicko");
band = Collections.unmodifiableList(band);

//stream
List<String> band = Collections
  .unmodifiableList(Stream.of("Bruce","Steve","Adrian", "Dave", "Janick","Nicko")
    .collect(toList()));
```

불변의 Collection이란 생성한 Collection을 수정할 수 없는 것을 말합니다.

- `java.util` 패키지는 `package-private ImmutableCollections` 클래스로 구성되어 있으며 불변 기능을 제공하는 클래스가 있습니다.
- 클래스의 인스턴스는 이미 존재하는 인터페이스의 정적 팩토리 메소드를 사용하여 만들어집니다.



### List

불변의리스트에는 추상 기본 클래스  `AbstractImmutableList<E>` 와 4 개의 구현이 있습니다.

-  `List0<E> ` 
-  `List1<E> ` 
-  `List2<E> ` 
-  `ListN<E> ` 

이러한 각 유형은 작성하는 데 사용되는 요소의 수에 해당합니다. `java.util.List` 인터페이스에는 위의 구현을 사용하여 불변의 객체를 생성하는 12가지 정적 팩토리 메소드가 있습니다.

```java
static <E> List<E> of()

static <E> List<E> of(E e1)

static <E> List<E> of(E e1, E e2)
  
...

static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6, E e7, E e8, E e9, E e10)

static <E> List<E> of(E... elements)
```

아래의 메소드를 사용하면 `UnsupportedOperationException`가 발생합니다.

```java
boolean  add ( E  e );
boolean  addAll ( Collection <?  extends  E >  c );
부울  addAll ( int  index , Collection <?  extends  E >  c );
void     clear ();
부울  remove ( Object  o );
부울  removeAll ( Collection <?>  c );
부울  removeIf ( 술어 <?  super  E >  필터 );
void     replaceAll ( UnaryOperator < E >  연산자 );
boolean  retainAll ( Collection <?>  c );
void     sort ( Comparator <?  super  E >  c );
```

내용을 불변하는것 외에도  `null` 값의 유효성도 체크합니다. 아래의 코드는 `NullPointerException`이 발생합니다.

```java
List<String> band = List.of("Bruce","Steve","Adrian", "Dave", "Janick", null);
```

아래는 불변 List를 만드는 예제입니다.

```java
List<String> band = List.of("Bruce","Steve","Adrian", "Dave", "Janick","Nicko");
```



### Set

`ist` 인터페이스와 비슷하게 구현되며. 추상 기본 클래스  `AbstractImmutableSet<E>` 와 네 가지 구현이 있습니다.

-  `Set0<E> ` 
-  `Set1<E> ` 
-  `Set2<E> ` 
-  `SetN<E> ` 

``java.util.List` 인터페이스에는 위의 구현을 사용하여 불변의 객체를 생성하는 12가지 정적 팩토리 메소드가 있습니다.

내용을 불변하는것 외에도  `null` 값의 유효성도 체크합니다. 아래의 코드는 `NullPointerException`이 발생합니다.

```java
Set<String> band = Set.of("Bruce","Steve","Adrian", "Dave", "Janick", null);
```

List와 다른 점은 중복된 값을 넣으려고 하면 `IllegalArgumentException`이 발생합니다.

```java
Set<String> guitarists = Set.of("Adrian", "Dave", "Janick", "Janick");
```

아래는 불변 Set을 만드는 예제입니다.

```java
Set<String> band = Set.of("Bruce","Steve","Adrian", "Dave", "Janick","Nicko");
```



### Map

불변의 객체를  `java.util.Map` 인터페이스의 static 팩토리 메소드를 사용하여 만들 수 있습니다. .

```java
static <K, V> Entry<K, V> entry(K k, V v)
```

불변의 `Map`에는 `AbstractImmutableMap<K, V> ` 3 개의 구현을 가지는 추상 기본 클래스가 있습니다   .

-  `Map0<K, V>`
-  `Map1<K, V>`
-  `MapN<K, V> `

`java.util.Map` 인터페이스 내부에는 다음과 같은 팩토리 메소드가  있습니다.

```java
static <K, V> Map<K, V> of()

static <K, V> Map<K, V> of(K k1, V v1)

static <K, V> Map<K, V> of(K k1, V v1, K k2, V v2)
...

static <K, V> Map<K, V> of(K k1, V v1, K k2, V v2, K k3, V v3, K k4, V v4,
                           K k5, V v5, K k6, V v6, K k7, V v7, K k8, V v8, 
                           K k9, V v9, K k10, V v10)

static <K, V> Map<K, V> ofEntries(Entry<? extends K, ? extends V>... entries)
```

`List`와 `Set`과는 다르게 최대 10개의 요소를 포함하는 불변 객체를 만들 수 있습니다.

아래의 메소드를 사용하면 `UnsupportedOperationException`가 발생합니다.

```java
void clear()
V compute(K key, BiFunction<? super K,? super V,? extends V> rf)
V computeIfAbsent(K key, Function<? super K,? extends V> mf)
V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> rf)
V merge(K key, V value, BiFunction<? super V,? super V,? extends V> rf)
V put(K key, V value)
void putAll(Map<? extends K,? extends V> m)
V putIfAbsent(K key, V value)
V remove(Object key)
boolean remove(Object key, Object value)
V replace(K key, V value)
boolean replace(K key, V oldValue, V newValue)
void replaceAll(BiFunction<? super K,? super V,? extends V> f)
```

불변의 `Map`을 만드는 것과 상관 없이 Key,Value또는 전체 요소가 null인 경우에는 인스턴스화 할 수 없습니다. 아래의 예제들은 `NullPointer`를 던지는 코드입니다.

```java
Map<String, Long> age = Map.of(null, 59L, "Steve", 61L);
Map<String, Long> age = Map.of("Bruce", null, "Steve", 61L);
Map<String, Long> age = Map.ofEntries(Map.entry("Bruce", 59L), null);
```

중복값을 가지는 Map을 생성하면 `IllegalArgumentException`가 발생합니다.

```java
Map<String, Long> age = Map.of("Bruce", 59L, "Bruce", 59L);
Map<String, Long> age = Map.ofEntries(Map.entry("Bruce", 59L),
                                      Map.entry("Bruce", 59L));
```

아래는 불변 Map을 만드는 예제입니다.

```java
Map<String, Long> age = Map.of("Bruce", 59L, "Steve", 61L, "Dave", 60L,
                               "Adrian", 60L, "Janick", 60L, "Nicko", 65L);
Map<String, Long> age = Map.ofEntries(Map.entry("Bruce", 59L),
                                      Map.entry("Steve", 61L),
                                      Map.entry("Dave", 60L),
                                      Map.entry("Adrian", 60L),
                                      Map.entry("Janick", 60L),
                                      Map.entry("Nicko", 65L));
```

### 결론

편리하게 불변의 객체를 만들 수 있고 Null과 중복 삽입을 사전에 방지할 수 있는 유용한 기능입니다.



### 앞으로 다뤄질 내용

- Java9에서 달라진점



