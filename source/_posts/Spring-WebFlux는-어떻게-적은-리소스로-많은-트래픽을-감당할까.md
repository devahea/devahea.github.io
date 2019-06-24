---
title: Spring WebFlux는 어떻게 적은 리소스로 많은 트래픽을 감당할까?
date: 2019-04-21 13:44:32
categories:
 - spring
tags:
- Spring WebFlux
- Event Driven
- Reactive Programming
author: 김민수
img: https://dvas0004.files.wordpress.com/2018/07/reactive_spring.png?w=648

---

# Spring WebFlux는 어떻게 적은 리소스로 많은 트래픽을 감당할까?
![Webflux Performance](https://3.bp.blogspot.com/-3i759KJap_U/We6baQQFc2I/AAAAAAAAfTs/0G7gLgD2BWsmVbPluFFoeGhViOafX1QqgCLcBGAs/s1600/Boot1VsBoot2.png)

위 그림은 [DZone](https://dzone.com/articles/raw-performance-numbers-spring-boot-2-webflux-vs-s) 게시글 중 하나인 Spring WebFlux를 이용한 Boot2와 Spring MVC를 이용한 Boot1을 비교한 그래프이다.

해당 그래프에서는 두 가지 특징을 볼 수 있다.
첫번째로는 유저가 적을 때에는 성능에 별반 차이가 없다. 두 번째로는 유저가 늘어나면 늘어날수록 극명한 성능 차이를 보여주고 있다. 어떻게 이런 차이가 일어날 수 있을까?

본글은 아래의 구성을 가지고 있다.

1. I/O
1. Event-Driven
1. Spring Framework

먼저, 1과 2를 통해서 원리를 알아본 다음 3에서 이를 접목시켜서 위의 그래프가 나올수 있는 이유를 설명할 것이다.

## I / O
**Why was Spring WebFlux created?**
>Part of the answer is the need for a non-blocking web stack to handle concurrency with a small number of threads and scale with fewer hardware resources. – Spring Document

핵심만 말하자면 non-blocking을 통해서 적은 수의 리소스로 동시성을 다룬다는 것이다. I/O의 원리부터 시작해서 non-blocking에 대해서도 이해해보도록 하자.

![IO](https://eunhyejung.github.io/assets/contents/content02.PNG)사용자가 I/O 요청을 할 때 CPU가 I/O Controller에 요청을 하고 I/O Controller가 파일을 다 가져오면 그것을 Memory에 적재시키고 CPU에게 완료되었다고 알려준다. 즉 큰 그림은 CPU -> I/O Controller -> CPU의 형태이다. 

핵심은 CPU가 I/O를 직접 가져오는 것이 아니라, 작은 CPU라고 불리는 I/O Controller가 수행한다는 이야기이다. 좀 더 나아가면 작업을 단순히 위임시키고 작업이 완료되는 동안에는 다른 일을 수행할 수 있다는 말이다. 이러한 예처럼 I/O를 처리하는데 몇 가지 방법이 있다. 

### Blocking I/O
![Blocking IO](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory&fname=http%3A%2F%2Fcfile9.uf.tistory.com%2Fimage%2F991CE83359A87B6D342482)가장 기본적인 I/O 모델이며 여러분들이 Spring MVC와 RDBMS를 사용하고 있으면 대부분 이 모델을 사용하고 있을 것으로 예상된다.

Application에서 I/O 요청을 한 후 완료되기 전까지는 Application이 Block이 되어 다른 작업을 수행할 수 없다. 이는 해당 자원이 효율적으로 사용되지 못하고 있음을 의미한다.

그러나 생각을 해보면 여러분들의 Application들은 Blocking 방식임에도 불구하고 마치 Block이 안된듯이 동작하는 것처럼 보인다. 이것은 여러분들이 Single Thread를 기반으로 하는 것이 아닌 Multi Thread를 기반으로 동작하기 때문이다. Block 되는 순간 다른 Thread가 동작함으로써 Block의 문제를 해소하였다. 그러나 Thread 간의 전환(Context Switching)에 드는 비용이 존재하므로 여러 개의 I/O를 처리하기 위해 여러 개의 Thread를 사용하는 것은 비효율적으로 보인다.

### Synchronous Non-Blocking I/O
![Synchronous Non Blocking IO](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F99245C3359A87B9533B7DA)
Application에서 I/O를 요청 후 바로 return되어 다른 작업을 수행하다가 특정 시간에 데이터가 준비가 다되었는지 상태를 확인한다. 데이터의 준비가 끝날 때까지 틈틈이 확인을 하다가 완료가 되었으면 종료된다.

여기서 주기적으로 체크하는 방식을 **폴링(Polling)** 이라고 한다. 그러나 이러한 방식은 작업이 완료되기 전까지 주기적으로 호출하기 때문에 불필요하게 자원을 사용하게 된다.

### Asynchronous Non-blocking I/O
![Async Non Blocking IO](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F99D8873359A87C3D1001BD)
I/O 요청을 한 후 Non-Blocking I/O와 마찬가지고 즉시 리턴된다. 허나, 데이터 준비가 완료되면 이벤트가 발생하여 알려주거나, 미리 등록해놓은 callback을 통해서 이후 작업이 진행된다.

이전 두 I/O의 문제였던 Blocking이나 Polling이 없기 때문에 자원을 보다 더 효율적으로 사용할 수 있다.
(이후로는 편의상 Non-Blocking I/O라고 하겠다.)

---

[Java 코드](https://github.com/viviennes7/io-example/blob/master/src/test/java/com/ms/ioexample/io/IOTest.java)를 통해서 이를 이해해보자.
### Blocking I/O
```java
@Test
public void blocking() {
    final RestTemplate restTemplate = new RestTemplate();

    final StopWatch stopWatch = new StopWatch();
    stopWatch.start();

    for (int i = 0; i < 3; i++) {
        final ResponseEntity<String> response =
                restTemplate.exchange(THREE_SECOND_URL, HttpMethod.GET, HttpEntity.EMPTY, String.class);
        assertThat(response.getBody()).contains("success");
    }

    stopWatch.stop();

    System.out.println(stopWatch.getTotalTimeSeconds());
}
```
Spring의 HTTP 요청 라이브러리인 `RestTemplate`을 사용하여 3초가 걸리는 API를 3번 호출하였다. 결과는 여러분도 알다시피 9.xx초가 나온다. 이유는 I/O가 요청 중일 때에는 아무 작업도 할 수 없기 때문이다.

### Non Blocking I/O
```java
@Test
public void nonBlocking3() throws InterruptedException {
    final StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    for (int i = 0; i < LOOP_COUNT; i++) {
        this.webClient
                .get()
                .uri(THREE_SECOND_URL)
                .retrieve()
                .bodyToMono(String.class)
                .subscribe(it -> {
                    count.countDown();
                    System.out.println(it);
                });
    }

    count.await(10, TimeUnit.SECONDS);
    stopWatch.stop();
    System.out.println(stopWatch.getTotalTimeSeconds());
}
```
WebFlux에서 제공하는 `WebClient`를 사용해서 위와 동일하게 3초가 걸리는 API를 호출하였다. for문 안의 변수인 `LOOP_COUNT`는 100으로 코드상에서 설정되어있다. 3초 걸리는 API를 100번 호출한다 하더라도 3.xx초 밖에 걸리지 않는다. 더 나아가서 `LOOP_COUNT`를 1000으로 변경하더라도 필자의 컴퓨터에서는 4.xx초 밖에 걸리지 않는다. Blocking I/O와 비교해봤을 때 정말 효율적이라고 볼 수 있다. 

만약, Blocking을 위처럼 많은 요청을 동시에 처리하려면 그 만큼의 Thread이 생성되어야 한다. 그러나 이렇게 처리한다 해도 Context Swiching에 의한 오버헤드가 존재할 것이다.

## Event-Driven
시장 조사 기관 가트너는 비즈니스 업계가 주목해야 할 2018 10대 전략기술 트렌드를 발표했고, Event-Driven가 포함되어 있다. 또한 Event-Driven을 토대로 많은 프레임워크와 라이브러리가 발전하고 있다. Spring WebFlux, Node.js, Vert.x 등이 그에 따른 예이다. 우리가 자주 접하는 기술들에 어떻게 스며들어 접목이 되었는지에 대해서 살펴보자.

Event-Driven Programming은 프로그램 실행 흐름이 이벤트(ex : 마우스 클릭, 키 누르기 또는 다른 프로그램의 메시지와 같은 사용자 작업)에 의해 결정되는 프로그래밍 패러다임이다. Event가 발생할 때 이를 감지하고 적합한 이벤트 핸들러를 사용하여 이벤트를 처리하도록 설계됐다. 순차적으로 진행되는 과거의 프로그래밍 방식과는 달리 유저에 의해 종잡을 수 없이 진행되는 GUI(Graphical User Interface)가 발전됨에 따라 Event-Driven 방식은 더욱더 많이 쓰이게 되었다. 

대표적으로 우리 자주 접할 수 있는 방식은 아래와 같은 Click Event이다. 

![Click Event](http://derickbailey.com/wp-content/uploads/2015/09/edit-click.jpeg)

아래는 Java를 이용하여 Click Event를 구현한 예이다.

```java
JButton button = new JButton();
button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println("clicked");
    }
});
```
물론 Lambda Expression 사용하면 아래처럼 간결하게 표현이 가능하다.
```java
JButton button = new JButton();
button.addActionListener(e -> System.out.println("clicked"));
```

우리는 Java 이외의 타 언어에서도 자연스럽게 `Listener`에 등록하여 Event를 구현하고 있다. 그런데 Button은 어떻게 유저에 의해 Click이 되었는지 **인지**할 수 있을까? 단순히 `Listener`에 등록하기만 하면 자동적으로 인지할까? 

그렇지 않다. 

과거로 돌아가 C언어로 키보드 Event를 핸들링하는 코드를 작성해보자.
```c
int main(void){
    char key;
    while(1){
        key = getch();      // (1) 

        switch (key) {      // (2)
            case 1 : 실행문; break;
            case 2 : 실행문; break;
            case 3 : 실행문; break;
            case 4 : 실행문; break;
            default : 실행문; break;
        }
    }
    return 0;
}
```
위 코드는 두 가지 경우로 되어있다.
(1) 무한루프를 돌면서 사용자에 의해 Key가 눌러지는 것을 감지한다.
(2) 감지된 값을 토대로 해야 할 일을 알맞은 곳에서 처리한다.

이를 일반화시켜 말하면 다음과 같다.
Event Loop가 돌면서 Event를 감지한 뒤 Event Handler 또는 Event Listener에게 보내서 작업을 처리한다.

Java Swing에서는 내부적으로 어떻게 Event를 처리할까?
![Swing](https://images.techhive.com/images/idge/imported/article/jvw/2003/06/jw-0606-swingworker1-100156726-orig.gif)

위의 c언어로 작성한 코드와 거의 동일하지 않은가? 단지 Event Queue만 추가되었을 뿐이다. 그러나 코드는 한편 간결해졌다. 
```java
JButton button = new JButton();
button.addActionListener(e -> System.out.println("clicked"));
```

일일이 Event를 제어했던 과거와는 달리 요즘은 이를 단순히 Listener에 행위만 등록해주면 간편하게 Event를 제어할 수 있다. 이는 Event Handle만이 관심의 대상이고 이에 집중할 수 있게 한다. 달리 말하면 Evevt Loop를 돌면서 요청을 감지하고 적합한 Handler에 위임해주는 부수적인 부분은 언어 레벨에서 처리를 해준다는 말이다.

이를 일반화하면 아래와 같은 이미지를 그릴 수 있다.
![Event-Driven](https://deepakpol.files.wordpress.com/2015/09/event-loop.png)
Event-Driven이라는 키워드가 언급되면 위의 이미지를 기억에서 꺼내면 된다.

그리고 이러한 Event 처리는 Server에도 적합하다. 왜냐하면 HTTP Request라는 **Event**가 발생하기 때문이다. 그래서 Node.js, Spring WebFlux, Vert.x 등은 Event-Driven 형태로 Architecture가 구현되어있다.

## Spring Framework
![Spring Model](https://spring.io/img/homepage/diagram-boot-reactor.svg)
위 그림처럼 Spring은 Reactive Stack과 Servlet Stack 두 가지 형태를 제공한다. 또한 Reactive Stack은 non-blocking I/O를 이용해서 많은 양의 동시성 연결을 다룰 수 있다고 한다. 과거로 돌아가서 Servlet Stack의 문제점을 파악하고 이를 어떻게 Reactive Stack으로 해결했는지 알아보자.

### Spring MVC
![Thread Pool Model](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2Fp5S5V%2Fbtqwg3zeAQP%2FtNjcoOvjL3LBOIRPa7Z61k%2Fimg.png)
위 그림처럼 유저들로부터 HTTP 요청이 들어올 때 요청들은 Queue를 통하게 된다. Thread pool이 수용할 수 있는 수(thread pool size)의 요청까지만 동시적으로 작업이 처리되고 만약 넘게 된다면 큐에서 대기하게 된다. 즉 하나의 요청은 하나의 Thread를 요구한다. (one request per thread model)

Thread pool은 다음과 같다. Thread를 생성하는 비용이 크기 때문에 미리 생성하여 재사용함으로써 효율적으로 사용한다. 그렇다고 과도하게 많은 Thread를 생성하는 것이 아니라 서버 성능에 맞게 Thread의 최대 수치를 제한시킨다. 참고로 tomcat default thread size는 200이다. 

그런데 만약 대량의 트래픽이 들어와 thread pool size를 지속적으로 초과하게 된다면 어떻게 될까?

![Queue](https://onacloud.co.za/wp-content/uploads/2016/07/queue-480x270.png)
설정해놓은 thread pool size를 넘게 되면 위 그림처럼 작업이 처리될 때까지 Queue에서 계속해서 기다려야 한다. 그래서 전체의 대기시간이 늘어난다. 이런 현상을 **Thread pool hell**이라고 한다.

아래 사진은 Linkedin의 Thread Pool Hell 현상에 대한 그래프이다. Thread pool이 감당할 수 있는 요청수를 넘는 순간부터는 평소보다 수배나 많은 지연시간을 보여준다. 
![Thread Pool Hell](http://image.slidesharecdn.com/theplayframeworkatlinkedin-final-130604033451-phpapp01/95/the-play-framework-at-linkedin-8-638.jpg)
Thread pool이 감당할 수 있을 때까진 빠른 처리속도를 보이지만, 넘는 순간부터는 지연시간이 급격하게 늘어난다.

하나의 작업이 늦게 처리되는 부분에 대해서도 우린 고민해 볼 필요가 있다. 독자분들이 만든 코드가 보통 왜 느려지는가? 특수한 경우를 제외하면 DB, Network 등의 I/O가 일어나는 부분에서 아마 시간을 많이 소비했을 것이다. 

설명했듯이 I/O 작업은 CPU가 관여하지 않는다. I/O Controller가 데이터를 읽어오고 이를 전달받을 뿐이다. 위에서 I/O를 처리하는 3가지 방식을 소개했는데 가장 효율이 좋은 방법은 마지막에 설명한 Asynchronous Non-blocking I/O이라고 하였다. Blocking 방식은 I/O Controller가 데이터를 읽는 동안 CPU가 아무 일도 할 수가 없고, Non-Blokcing방식은 polling 때문에 불필요하게 CPU를 소비한다고 했다. 

Spring에서도 Non-blocking I/O를 이용해서 효율적으로 작업을 처리할 수 있는 방법을 제공한다. 그 수단이 WebFlux이다.

### Spring WebFlux
![Webflux Event Driven](https://workwiththebest.intraway.com/wp-content/uploads/sites/4/2018/01/TRM-10-FINAL.png)
위는 전반적인 WebFlux의 구조이다. 사용자들에 의해 요청이 들어오면 Event Loop를 통해서 작업이 처리가 된다. one request per thread model과의 차이점은 다수의 요청을 적은 Thread로도 커버할 수 있다.  worker thread default size는 서버의 core 개수로 설정이 되어있다. 즉 서버의 core가 4개라면 worker thread는 4개라는 말이며 이 적은 Thread를 통해서도 traffic을 감당할 수 있다. 위에서 하나의 Thread로 3초가 걸리는 API 1000개를 호출했음에도 4초밖에 안 걸렸다는 걸 상기시키면 이해에 도움이 될 것이다. 또한 비슷한 Architecture를 가진 node.js가 이미 증명을 하고 있다.

이렇듯 Non Blocking 방식을 활용하면 좀 더 효율적으로 I/O를 제어할 수 있고 성능에도 좋은 영향을 미친다. 특히나 유행하는 MSA에서는 수많은 Microservice가 거미줄처럼 서로를 네트워크를 통해서 호출하고 있다. 즉 많은 수의 Network I/O가 발생할 텐데 이를 Non Blocking I/O를 통해 좀 더 성능을 끌어올릴 수 있다.  

그러나 물론 제한된 점이 있다. WebFlux로 성능을 최대치로 끌어올리려면 모든 I/O 작업이 Non Blocking 기반으로 동작해야 된다. Blocking이 되는 곳이 있다면 안 하느니만 못한 상황이 되어버린다. 
예를 들어 멀티코어로 가정을 해보자. 그럼 처리할 수 있는 Thread는 2개인데 Blocking이 걸리는 API를 열명 이서 동시에 호출한다면 결국엔 Spring MVC처럼 8명이 I/O 작업이 끝날 때까지 기다려야 하는 구조가 되어버리기 때문이다.

> What if you do need to use a blocking library? Both Reactor and RxJava provide the publishOn operator to continue processing on a different thread. That means there is an easy escape hatch. Keep in mind, however, that blocking APIs are not a good fit for this concurrency model. - Spring Document

대안으로 `publicOn()`을 사용하라고 하지만 마지막 문장에서 Blocking은 이 모델에 적합하지 않다고 한다.

Java 진영에는 아쉽게도 정식으로 DB connection을 non-blokcing으로 정식으로 지원하는 라이브러리가 존재하지 않다. 다만 [R2DBC](https://github.com/r2dbc/r2dbc-postgresql)처럼 개발이 진행 중인 라이브러리들과 MongoDB, Redis 등의 NoSQL은 지원중이다.
또한 소수의 Thread에 의해서 수많은 요청을 처리하고, 순서대로 작업이 처리되는 것이 아니라 Event에 기반하여 실타래가 엉킨 것처럼 작업이 처리되기 때문에 트래킹 하기에 힘이 들다는 문제가 있다.

그렇다면 <strong>성능이 좋으니 무조건 WebFlux를 사용해야 할까?</strong>
지금까지 필자의 글에 혹했다면 위의 질문처럼 잘못된 생각을 할 수도 있다.
![Webflux Performance](https://3.bp.blogspot.com/-3i759KJap_U/We6baQQFc2I/AAAAAAAAfTs/0G7gLgD2BWsmVbPluFFoeGhViOafX1QqgCLcBGAs/s1600/Boot1VsBoot2.png)
위 그림은 Spring MVC나 Spring WebFlux 둘 다 성능이 동일한 구간이 있다. 서버의 성능이 좋으면 좋아질수록 해당 구간은 더 늘어날 것이다. 그렇기에 만약 여러분의 환경이 해당 구간이라면 굳이 사용할 필요가 없다. 또한 [Spring Document](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-framework-choice)에서는 동기 방식이 코드 작성, 이해, 디버깅하기 더 쉽다고 한다. 이 말은 즉 높은 생산성을 가진다는 말과 같은 것으로 보인다. 그렇기에 이해타산을 잘 따져서 선택해야 할 필요가 있다.

그리고 우리는 이제 왜 성능이 동일한 구간이 생기는 지를 알 수 있다. 저 구간은 바로 Thread Pool이 감당할 수 있을 정도의 요청이었기에 비동기적으로 잘 수행하다가 이후에는 Queue에 쌓여 점점 성능이 느려졌던 것이다.

'Spring WebFlux는 어떻게 적은 리소스로 많은 트래픽을 감당할까?'란 궁금증을 시작으로 여기까지 왔다. 이에 대한 답은 I/O를 Non Blockkng을 이용하여 잘 사용하는 것과 Request를 Event-Driven을 통해서 효율적으로 처리하기 때문에 가능하다.

참고 자료

* [Blocking / Non Blocking example Github](https://github.com/viviennes7/io-example)
* [Web on Reactive Stack](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-reactive-spring-web)
* [Spring.IO](https://spring.io/)
* [Dzone WebFlux Benchmarking](https://dzone.com/articles/raw-performance-numbers-spring-boot-2-webflux-vs-s)
* [컴퓨터 시스템 동작원리](https://eunhyejung.github.io/os/2018/06/29/operatingsystem-study02.html)
* [가트너 10대 기술 트렌드](http://www.techsuda.com/archives/10174)
* [요즘 제일 '핫'하다는 이벤트-드리븐... 누구냐, 넌! | SAMSUNG NEWSROOM](https://news.samsung.com/kr/%EC%9A%94%EC%A6%98-%EC%A0%9C%EC%9D%BC-%ED%95%AB%ED%95%98%EB%8B%A4%EB%8A%94-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%93%9C%EB%A6%AC%EB%B8%90-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C)
* [Event Driven and Reactive Architecture ‒ Deepak Pol's Blog ](https://deepakpol.wordpress.com/2015/09/29/event-driven-and-reactive-architecture/)

* [Blocking, Non-Blocking / Synchronous, Asynchronous](http://wiki.sys4u.co.kr/pages/viewpage.action?pageId=7767390)
* [blocking, non-blocking and Async](https://asfirstalways.tistory.com/348)
* [blocking vs non-blocking / synchronous vs asynchronous ](https://brainbackdoor.tistory.com/26)
* [Java Swing Architecture](https://www.javaworld.com/article/2073477/customize-swingworker-to-improve-swing-guis.html)
* [Reactive Web - Servlet & Async, Non-blocking I/O ](https://www.slideshare.net/arawnkr/reactive-web-servlet-async-nonblocking-io-73838876)
* [Testing Reactive Microservices With Spring](https://workwiththebest.intraway.com/white-paper/Testing-Reactive-Microservices-With-Spring-Webflux/)
* [Going Reactive with Spring, Coroutines and Kotlin Flow](https://spring.io/blog/2019/04/12/going-reactive-with-spring-coroutines-and-kotlin-flow)

* [요즘 제일 '핫'하다는 이벤트-드리븐… 누구냐, 넌! | SAMSUNG NEWSROOM](https://news.samsung.com/kr/%EC%9A%94%EC%A6%98-%EC%A0%9C%EC%9D%BC-%ED%95%AB%ED%95%98%EB%8B%A4%EB%8A%94-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%93%9C%EB%A6%AC%EB%B8%90-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C)
* [Event Driven and Reactive Architecture – Deepak Pol's Blog](https://deepakpol.wordpress.com/2015/09/29/event-driven-and-reactive-architecture/)
* [Observer Pattern](https://dbse-teaching.github.io/isee2018-SOverS/2018/05/25/System-Design.html)
* [blocking, non-blocking and Async](https://asfirstalways.tistory.com/348)
* [blocking vs non-blocking / synchronous vs asynchronous](https://brainbackdoor.tistory.com/26)
* [http://wiki.sys4u.co.kr/pages/viewpage.action?pageId=7767390](http://wiki.sys4u.co.kr/pages/viewpage.action?pageId=7767390) 
* [Reactive Web - Servlet & Async, Non-blocking I/O](https://www.slideshare.net/arawnkr/reactive-web-servlet-async-nonblocking-io-73838876)
* [Testing Reactive Microservices With Spring-Webflux - Work with the Best](https://workwiththebest.intraway.com/white-paper/Testing-Reactive-Microservices-With-Spring-Webflux/)
* [Going Reactive with Spring, Coroutines and Kotlin Flow
](https://spring.io/blog/2019/04/12/going-reactive-with-spring-coroutines-and-kotlin-flow)
