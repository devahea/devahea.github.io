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



## JDK 출시 및 지원 기간

![](/images/java9/oracleJdk.gif)

JDK 출시 및 지원기간에 대해 알아보겠습니다.
파란색 마커는 무료 버전 OpenJDK의 출시시기 및 지원 기간을 나타내고 주황색 마커는 유료 버전 Oracle JDK를 뜻합니다.무료 OpenJDK 6 개월마다 메이저 버전 업이 등장하고 그 때마다 지원 기간이 종료하는 것을 알 수 있습니다.
Java 11부터 Java는 3 년마다 "LTS"(Long Term Support)라는 장기 지원에 대응 한 메이저 버전이 등장합니다. 그 LTS 버전마다 Oracle JDK의 메이저 버전 업을 합니다.

메이저 버전 업 후에는 이전 주요 버전에 대한 마이너 버전 업을하지 않게됩니다.



![](/images/java9/java-version.png)

왜 Java는 6 개월마다 메이저 버전 업으로 출시 모델을 변경하는 걸까요? 
Java는 지금까지 큰 기능 추가에 따라 메이저 버전 업을 해왔습니다. 큰 새로운 기능의 개발에 오랜 시간이 걸리고 때로는 개발 일정 지연도 있었습니다. 예를 들어, Java 6에서 7로 메이저 버전 업에는 4 년 8 개월, Java 7에서 8로 2 년 8 개월, Java 8-9로는 3 년 6 개월 걸렸습니다. 최근에는 Java는 다른 언어 나 기술과 비교하면 진화가 느린 다소 오래된 것으로 보입니다.기존 출시 모델을 바꾸게 6 개월마다로 전환 한 것은 이러한 점에서 Java를 지금까지보다 빠른 속도로 전진 시키려고하고 있기 때문입니다.



## 마이그레이션시 우려 사항

많은 개발자들이 Java8에서 Java9로 업그레이드 할 때 많은 변경으로 인해 애플리케이션이 중단될수 있다는 걱정이 있습니다. 변경 사항 중 하나는 내부 API의 캡슐화 입니다. 애플리케이션에서 내부 API를 사용하지 않는 경우 Java9 이상으로 마이그레이션 하는것은 어렵지 않습니다.



## 알아두면 좋을 변경된 점



### **HTTP 2 Client**

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



### More Concurrency Updates (reactive stream)

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



## Convenience Factory Methods for Collections

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



## Reactive Streams

Java 9 에서 Reactive Streams API가 추가되었습니다.

- Reactive Streams 추가
  - publish-subscribe 프레임 워크를 지원하는 인터페이스
- reactive stream API 
  - java.util.concurrent.Flow
  - java.util.concurrent.Flow.Publisher
  - java.util.concurrent.Flow.Subscription
  - java.util.concurrent.Flow.Subscriber
  - java.util.concurrent.Flow.Processor

리액티브 프로그래밍이란 무엇일까요?
많은 응용 프로그램에서 데이터는 거의 실시간으로 처리됩니다. 대부분의 경우  데이터가 있을 때를  미리 알 수 없습니다. 이러한 비동기식 데이터 처리를 용이하게하기 위해  능률적인 방법을 사용해야합니다.

Flow API (및 Reactive Streams API)는 Iterator 및 Observer 패턴의 아이디어를 조합 한 것으로 볼 수 있습니다.

자세한 내용은 [여기서](https://ahea.wordpress.com/2017/02/02/iterable%ec%99%80-observable%ec%9d%98-%ea%b0%9c%eb%85%90/) 확인할 수 있습니다.



![](/images/java9/flow.png)

Reactive Streams 인터페이스의 표준 Java 소스는 java.util.concurrent.Flow 클래스에 있으며 Flow클래스 내에 정적 인터페이스로 제공됩니다 . JavaDoc이 제거되면 Flow클래스는 다음과 같이 정의됩니다.
Observer패턴에서 문제가 있다는 부분이 onError와 onComplete로 해결됩니다.에러가 난다면 onError, 다 끝났다면 onComplete가 호출됩니다.
리 액티브 스트림은 (1) 게시자, (2) 구독자, (3) 구독 및 (4) 프로세서의 네 가지 기본 엔터티로 구성됩니다.



### 게시자(Publishers), 구독자(Subscriber)

![](/images/java9/ReactiveStreams_1.png)

게시자, 구독자 및 구독(Publishers, Subscribers, and Subscriptions) 의 구조에 대해 알아보겠습니다.
Reactive Programming 모델에는 게시자와 구독자가 있습니다. 게시자는 구독자가 비동기 적으로 구독하는 데이터 스트림을 게시합니다.
게시자와 구독자 간의 양방향 연결을 **구독**이라고합니다.
구독자가 게시자를 구독하면 게시자는 구독을 구독자에게 알리고 구독자가 구독에 대한 참조를 저장할 수있게합니다. 이 통지 프로세스가 완료되면 구독자는 게시자에게 항목을 받을 준비가 되었음을 알릴 수 있습니다



![](/images/java9/subscriber.png)

그중에서 게시자와 구독자에 대한 예제를 통해 간단히 살펴보겠습니다.
onSubscribe는 최초 1번만 호출되는 메소드onNext는 기존 Observer에서 update와 같은 역활
Java 버전에는 게시자 구현이 포함되어 SubmissionPublisher있습니다. 이 SubmissionPublisher클래스는 submit(T item)메서드를 사용하여 구독자에게 푸시 할 항목을 허용하는 단순한 게시자 역할을합니다 . 항목이 submit 메서드에 제출 되면 다음과 같이 구독자에게 비동기 적으로 푸시됩니다.
구독이 설정되면 값 0을 9게시자에게 제출 하고 구독자에게 값을 비동기 적으로 푸시합니다. 그런 다음 구독자는 값을 표준 출력하고 각 항목을 처리하고 다른 값을 받아 들일 준비가되었음을 구독에 알립니다. 그런 다음 비동기 전송이 완료 될 수 있도록 1 초 동안 주 스레드를 일시 중지합니다. 이 submit 메서드는 제출 된 항목을 구독자에게 푸시합니다.
아래 그림과 같이 출력되는것을 확인할 수 있습니다.

![](/images/java9/subscriber2.png)





### **프로세서(Processor)**

![](/images/java9/ReactiveStreams_3.png)

엔터티가 게시자와 구독자 인 경우 이를 프로세서 라고합니다 . 프로세서는 일반적으로 다른 게시자와 구독자 (둘 중 하나가 다른 프로세서 일 수 있음) 간의 중개자 역할을하여 데이터 스트림에서 일부 변형을 수행합니다. 
예를 들어 구독자로 전달하기 전에 일부 조건과 일치하는 항목을 필터링하는 프로세서를 만들 수 있습니다. 프로세서의 시각적 표현은 그림과 같습니다.



![](/images/java9/processer.png)

![](/images/java9/processer1.png)

프로세서를 추가하는 예제입니다.프로세서는 구독자로 전달하기 전에 항목을 필터링할 수 있다고 말씀 드렸습니다.소스를 보시면 onNext메소드에서 넘겨온 item필드에 대해 10씩 증가하였습니다.
프로세서를 추가하는 방법은 프로세서 객체를 생성 후 구독자를 추가합니다.

![img](/images/java9/processer2.png)

10씩 증가되서 출력된 부분을 확인하실 수 있으며 프로세서가 먼저 종료 된것을 확인 할 수 있습니다.



## **Project Jigsaw**

![](/images/java9/module.png)

Java9에서 크게 달라진점 중 하나인 모듈 시스템으로 자신만의 모듈을 만들수 있습니다

Java 플랫폼의 모듈화와 일반 모듈 시스템의 도입 인 Project Jigsaw는 Java 9에 포함되었습니다. java9에서 적용된 모듈에 대해 알아보겠습니다.모듈화는 구성 요소 간의 느슨한 커플 링 구성 요소 간의 종속성을 명확히하고강력한 캡슐화를 사용한 숨겨진 구현을 하는 것을달성하는 데 도움이되는 설계 원칙입니다. 



![](/images/java9/module_JAVA8.png)

![](/images/java9/module_JAVA9.png)

Java Home 폴더에 bin, jre, lib 등의 디렉토리를 볼 수 있습니다. JDK 9에는 JRE가 포함되어 있지 않습니다.  JDK 9에서는 JRE가 별도의 배포 폴더로 분리됩니다. JDK 9 소프트웨어에는 "jmods"라는 새 폴더가 있습니다.  Java 9 모듈 집합을 포함합니다. JDK 9에서는 rt.jar이없고 Tools.jar이 없습니다.



### **Module System이 필요한 이유**

왜 모듈성이 필요한가? 

- 작은 장치가 불필요하게 전체 JDK를 실행하지 않도록합니다.
- 작은 컴퓨터 디바이스에서 경량화하여 효과적으로 사용해야합니다. 
- 캡슐화시 결함을 제거합니다. Java 9에서는 기본적으로 모든 패키지는 모듈 비공개이기 때문에 public 선언으로 module-info.java로 반출되지 않으면 더 이상 볼 수 없습니다. 의존성 (컴파일 시간과 런타임 모두)을 검사합니다. 리플렉션을 사용해도 런타임 때 액세스 할 수 없습니다. 

모듈화는 언어 레벨에서 모듈간의 의존성을 알 수 있으니 애플리케이션이 사용하는 모듈만 모아서 런타임 환경(이미지)를 만들 수 있습니다.필요한 모듈만으로 경량화된 이미지를 만들기 위함입니다



### **Module화 된 JDK**



모듈화 된 JDK를 구조를 살펴보겠습니다.그래프 하단에 java.base가 있습니다. 생성하는 모든 모듈 은 java.lang.Object 의 암시적인 확장과 유사하게 선언 여부에 관계없이 java.base를 읽습니다
JDK의 모듈화는 사용하려는 Java Runtime의 모듈을 지정할 수 있음을 의미합니다.





## **Private Interface Method**

![](/images/java9/private-method.png)

JAVA8 에서는 특정 기능을 처리하는 내부method 일뿐인데도 외부에 공개되는 public method 로 만들어야 하는 단점을 가지고 있었습니다그러한 요구 사항과 interface를 구현하는 다른 interface 또는 class가 해당 method에 액세스하거나 상속 할수 있는것을 원하지 않는 요구사항이 있다.Java9 Private Interface Method 에서는 **interface 에 private method / private static method라는 새로운 기능을 제공**하여 문제를 해결한다. 이제 중복 코드를 피하고 interface에 대한 캡슐화를 유지 할 수 있다.



## **try-with-resource 향상**

![](/images/java9/try1.png)

![](/images/java9/try2.png)

기존의 java7 부터 제공된 try-with-resource 구문이 불편함이 많았습니다.Try-With-Resources 외부에서 선언 된 리소스를 사용할 수 없습니다.(JAVA7,8)final or effectively final이 적용되어 참조 변수를 구문에 바로 사용할 수 있게 되었습니다.



## **Improve Diamond Operator**

JAVA 7 ![](/images/java9/diamond1.png)

JAVA 9 ![](/images/java9/diamond2.png) 

Java7 에는 코드를 보다 읽기 쉽게 만드는데 도움되는 "<>"(다이아몬드 연산자) Diamond Operator 라는 새로운 기능이 있었지만 **익명 클래스(Anonymous Inner Class)에는 제한적**이었다.
**이제는 익명 클래스에서  Diamond Operator 를 사용할수 있습니다.**



## **Stream Improvements**

Java 8에는 개발자가 일련의 객체에서 집계 연산을 수행하는 데 도움이되는 Streams가 도입되었습니다. Java 9 에서는  **4가지** 새로운 추가 메소드를 사용하여 Stream 에서 반복에 대한 제어를 못함에 어려운 부분이 많았던 부분을 해소했습니다.
기본적으로 predicate functional interface를 변수로 제공하고 있습니다.



### takewhile 

![](/images/java9/takewhile.png)

takewhile은 false를 리턴 할 때까지 모든 값을 취합니다.출력된것을 보시면 빈값이 있기 전인 abc가 출력된것을 확인할 수 있습니다.



### dropwhile 

![](/images/java9/dropwhile.png)

dropWhile 메소드는  true를 리턴 할 때까지 시작시 모든 값을 버립니다. 지정된 조건과 일치하는 요소를 삭제 한 후에 이 스트림의 나머지 요소로 구성된 스트림을 리턴합니다.



### iterate 

![](/images/java9/iterate.png)

iterate매개 변수로 hasNext 조건자를 갖습니다. iterate 메소드는 이제 hasNext 조건자에서 false를 반환하면 루프를 중지시킵니다. 2부터 10보다 작은 수 까지 2씩 증가 시키는 예제를 만들었습니다. 2,4,6,8이 출력된것을 확인할 수 있습니다.



### ofNullable 

![](/images/java9/ofNullable.png)

NullPointerException을 방지하고 스트림에 대한 null 검사를 방지하기 위해 ofNullable 메서드가 도입되었습니다. 이 메소드는 단일 요소가 포함 된 순차적 스트림을 리턴하고, 그렇지 않으면 null이 아닌 경우 빈 스트림을 리턴합니다.
출력을 보면 값이 존재 할때는 1이 없을때는 0이 출력된것을 보실수 있습니다.



## **Optional class Stream Implementation**

때때로 우리는 Optional이 비어 있을 때 Optional을 반환하는 다른 액션을 실행하기를 원합니다 .  Java9에서는 Optional 클래스에 대해 세 가지 유용한 메소드를 추가하였습니다. 



### of()

![](/images/java9/of.png)

of() 메소드는 orElseGet()나 orElse()와 달리 Optional 객체를 리턴하고 싶을 때 사용합니다.



### ifPresentOrElse()

![](/images/java9/ifPresentOrElse.png)

기존의 ifPresent() 메서드는 Optional 객체가 값을 담고 있을 때 처리할 때 사용했습니다. ifPresentOrElse() 메서드는 Optional 객체가 비어있을 경우 처리할 내용까지 정의할 수 있습니다.



### stream()

![](/images/java9/stream.png)

Stream() 메소드는 Optional 객체를 Stream 객체로 변환하기 위해서 사용합니다.비어있는 Optonal 객체는 제외하고 이름들을 List 객체로 변환하는 코드입니다.



## **Local-Variable Type Inference**(JDK10)

자바 9까지는 로컬 변수의 타입을 명시 적으로 언급하고 그것을 초기화하는 데 사용 된 이니셜 라이저와 호환되는지 확인해야했습니다.

![](/images/java9/var.png)

자바10에서는로컬변수를 선언할때 타입추론을 이용하여 명시적으로 타입선언 없이도 변수를 선언할수 있게 되었습니다.
이 기능은 코드를 줄이는 데 도움이 될거같습니다.



### **컴파일 오류**

![](/images/java9/varCompile.png)

변수의 타입은 컴파일시에 추론되며 나중에 변경할 수 없습니다.
멤버 변수, 메서드 매개 변수 형식 등에는 사용할 수 없습니다. 
앞에서 언급했듯이, var 는 이니셜 라이저 없이null로 초기화하면 에러가 발생합니자람다 식은 명시 적 대상 유형을 필요로하므로 var을 사용할 수 없습니다. 배열 이니셜 라이저도 마찬가지입니다.

이 기능은 이니셜 라이저가있는 로컬 변수에서만 사용할 수 있습니다. 



### 잘 사용 하기

var를 합법적으로 사용될 수있는 상황이 있지만 그렇게 하는 것이 좋지 않을 수 있는 경우가 있습니다
예를 들어, 코드가 읽기 어렵게 될 수있는 상황이 있습니다.



![](/images/java9/var1.png)

여기서는 var를 합법적으로 사용하더라도 코드를 읽을 수 없게 만드는 메소드 호출에 의해 반환되는 유형을 이해하기 어렵게 됩니다.

![](/images/java9/var2.png)

Java 7에서 소개 된 다이아몬드 연산자와 함께 사용하면 Object로 타입으로 반환됩니다. 원하는 타입의 유형으로 선언하는것이 좋습니다.



## 참고자료

- http://www.publickey1.jp/2018/java101112b.gif
- https://pbs.twimg.com/media/DOGTWsrXcAA_7H8?format=jpg&name=small
- https://openjdk.java.net/projects/jdk9/
- https://dzone.com/articles/an-introduction-to-http2-support-in-java-9
- https://dzone.com/articles/reactive-streams-in-java-9
- https://www.journaldev.com/20723/java-9-reactive-streams
- https://openjdk.java.net/projects/jigsaw/spec/sotms/
- https://dzone.com/articles/java-9-modularity-jigsaw
- https://howtoprogram.xyz/2017/10/19/java-9-http-2-client-api-example/
- https://howtodoinjava.com/java9/java9-private-interface-methods/
- [https://twitter.com/java/status/928185028714160128/photo/1?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E928185028714160128&ref_url=https%3A%2F%2Ffuturecreator.github.io%2F2018%2F09%2F29%2Fjava-11-released%2F](https://twitter.com/java/status/928185028714160128/photo/1?ref_src=twsrc^tfw|twcamp^tweetembed|twterm^928185028714160128&ref_url=https%3A%2F%2Ffuturecreator.github.io%2F2018%2F09%2F29%2Fjava-11-released%2F)
- https://www.journaldev.com/13106/java-9-moduleshttps://blog.codecentric.de/en/2015/11/first-steps-with-java9-jigsaw-part-1/
- https://devahea.github.io/2017/02/02/iterable-ec-99-80-observable-ec-9d-98-ea-b0-9c-eb-85-90/