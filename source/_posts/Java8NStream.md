---
title: Java8 빨대 사용법.
date: 2019-04-20 16:42:01
tags:
---

## Java 와 Stream API
![Alt text](/images/stream0/java8.PNG)
Java8 에서 추가된 기능 중 하나인 Stream 은 왜 나오게 된 것 일까요?
Stream을 이용하는 간단한 예제를 보겠습니다.
```java
for(int i = 0; i<100; i++>){
    System.out.println(i);
}
```
이 코드를 stream 을 이용하면 다음과 같이 바뀝니다.
```java
IntStream.range(0, 100).forEach(System.out::println);
```
한줄로 위의 For문이 변경 되었습니다. Stream을 사용 하니깐 코드도 짧아지고 너무 좋네요.
그렇다면, 성능적으로는 어떨까요?
![https://blog.codefx.org/java/stream-performance/](/images/stream0/streamPerformence.PNG)
성능에 대해서는 구글링을 해보면 stream 을 사용하는 것이 성능적으로 더 떨어 진다는 결과가 많이 나왔습니다.
그렇다면, stream은 성능적으로 떨어지는데 왜 사용 하는 것일까요?
## Spring 과 Stream
Spring Data 에서는 다음과 같은 코드를 작성합니다.
```java
@Query("select u from User u")
Stream<User> findAllByCustomQueryAndStream();

Stream<User> readAllByFirstnameNotNull();

@Query("select u from User u")
Stream<User> streamAllPaged(Pageable pageable);
```
스프링 프레임워크에서는 느린 Stream을 사용하고 있군요!

Stream은 대체 무엇이고 언제 어떻게 사용해야 하는것일까요?

Stream에 대해 알아보고 위와 같은 궁금증을 함께 해결해보겠습니다

### 앞으로 다루어질 내용
- stream 이란
   - Sequence of elements
   - Source
   - Aggregate operations
- stream의 특징
   - pipelining
   - internal iteration
- Parallel stream 을 통한 병렬처리
- Spring 에서 Stream 사용하기