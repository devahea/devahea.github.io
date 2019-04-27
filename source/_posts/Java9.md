---
jtitle: JAVA8말고 JAVA9를 사용해볼까
date: 2019-04-23 03:07:13
tags: 
    - java
    - java9
img: /images/java9/java9.jpg
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



게시자 구현이 포함되어 `SubmissionPublisher`

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



 프로세서를 도입하여 게시자와 구독자를 프로세서와 연결

- 수신 된 값을 10 씩 증가시키고 증가 된 값을 구독자에게 푸시 (push)하는 프로세서

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



프로세서를 도입하고 원래의 게시자와 구독자를이 프로세서와 연결할 수 있습니다. 다음 예제에서는 수신 된 값을 10 씩 증가시키고 증가 된 값을 구독자에게 푸시 (push)하는 프로세서를 생성합니다.

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



### 앞으로 다뤄질 내용

- Java9에서 달라진점



