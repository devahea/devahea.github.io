---
title: Reactive Streams
url: 235.html
id: 235
categories:
  - reactive
date: 2017-02-13 13:04:22
tags:
---

Reactive 글 모음

1.  [Iterable와 Observable의 개념](https://ahea.wordpress.com/2017/02/02/iterable%ec%99%80-observable%ec%9d%98-%ea%b0%9c%eb%85%90/)
2.  [Reactive History](https://ahea.wordpress.com/2017/02/03/reactive-history/)
3.  [Reactive Streams](https://ahea.wordpress.com/2017/02/13/reactive-streams/)
4.  [RxJava](https://ahea.wordpress.com/2017/02/13/rxjava/)
5.  [Spring Reactive](https://ahea.wordpress.com/2017/02/15/spring-reactive/)
6.  [Reactive PublishOn, SubscribeOn](https://ahea.wordpress.com/2017/02/20/reactive-publishon-subscribeon/)

* * *

Observable의 문제
==============

기존의 Observable을 살펴봅시다. ![](http://ahea.files.wordpress.com/2017/02/40faf85dfe4048cf6074a6c6490b9830.png) Observable을 상속받은 Observable은 say가 호출되면 등록된 옵저버들에게 dataArr를 하나씩 보내줍니다 ![](http://ahea.files.wordpress.com/2017/02/5d33887faceb51a00668523be9cb44eb.png) 그러면 등록된 N개의 옵저버들은 update라는 메소드에서 Obsavable과 보내준 데이터를 받게 됩니다.그러면 옵저버는 각자의 역활에 맞게 작업을 수행하면 되는 것이죠. 이때 몇가지 문제가 있습니다.1. 데이터가 다 왔는지 끝을 알수 없다.Observable의 기본적인 구조에서는 지금 날라온 데이터가 마지막 데이터인지 알수 없습니다. 물론 마지막이라는것을 표시해 줄수있는 토큰이나 데이터를 보냄으로써 이 문제를 처리할 수는 있지만 구조상 그런 기능을 제공해주고 있지는 않습니다.2. Exception의 처리만약 Observable에서 데이터를 처리하는데 Exception이 났을경우가 있습니다. 예를 들면 지금 예제에서는 단순히 고정된 List타입을 가지고 설명을 했지만 Database에서 값을 가져온다던지, 파일에서 값을 가져온다던지, Web에서 Request를 받는다던지 할수 있습니다. 이때 Exception이 발생할경우 이 에러는 어디서 처리해야 할까요. 상황에 따라서는 Observable에서 단독적으로 처리할 수도 있겠지만 Observer들은 에러가 났는지 알수가 없습니다. Reactive Streams는 Observable한 구조에서 이와같은 문제를 해결하려고 합니다. 물론 이와 같은 문제 이전에 비동기/동기 문제를 쉽게 해결해주고 이와같은 문제를 해결해주는 것도 하나의 중요한 초점이지만 이 문제는 Observable이 같은 특징에서 확장된 부분이기 때문에 설명하지 않았습니다.

Reactive Streams 표준
===================

여러 기업들이 모여 Reactive Stream에 대한 표준을 정의하였습니다 (자세한 내용은 [http://www.reactive-streams.org/](http://www.reactive-streams.org/))Reactive Stream의 스펙을 보면서 위의 문제를 어떻게 해결하였는지 확인하겠습니다. Reactive Stream는 구현체가 아니라 Reactive Stream을 구현하기 위한 스펙(API, 또는 Java에서는 Interface)입니다. 이 스펙에서는 4개의 interface spec이 나옵니다.

*   Subscriber
*   Publisher
*   Subscription
*   Processor(얘는 Subscriber, Publisher를 상속한 아이입니다)

첫번째로 Subscriber는 Observer입니다. 기존에 Observer는 Observable에 등록을 하여 데이터를 받는 역활이였습니다. 기존에 Observer와 다른점을 메소드를 보면서 확인하겠습니다.

public interface Subscriber {
    public void onSubscribe(Subscription s);
    public void onNext(T t);
    public void onError(Throwable t);
    public void onComplete();
}

Subscriber는 4개의 메소드를 가지며 필수적으로 implement는 하여야 합니다. 구현은 스펙에 따라 하면 되는데 스펙의 디테일한 부분은 ([https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.0/README.md#specification](https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.0/README.md#specification)) 에서 확인하면 됩니다. 이 글에서모두 설명하기에는 길기 때문에 영어가 안되도 스펙을 한번 보시면 좋습니다. onSubscribe는 최초 호출되는 메소드입니다. Subscriber를 사용할때에는 무조건 처음에 호출해야 하는 내용이 스펙에 등록되어있습니다. onNext는 기존 Observer에서 update와 같은 역활을 합니다. 데이터를 받을 때 사용됩니다. onNext는 여러번 호출될수가 있습니다. 물론 여러번 호출 되기 전 onSubscribe가 한번은 호출되야 합니다. 바로 위에 설명했듯이 Subscriber를 사용하려면 무조건 처음에는 onSubscribe가 호출되야 하는 스펙 내용이 있기 때문입니다. Observer패턴에서 문제가 있다는 부분이 onError와 onComplete로 해결됩니다. 메소드 이름에서 딱 보이다 싶히 에러가 난다면 onError, 다 끝났다면 onComplete가 호출됩니다. onError와 onComplete가 호출되면 Observable이 데이터를 더이상 보내지 않습니다( 정확히는 스펙상 보내지 않아야 합니다) 순서를 보면 이렇습니다 ![](http://ahea.files.wordpress.com/2017/02/9afe0ddaa7613807908d01413dc32859.png) 해당 내용을 spec에서는 이렇게 보여주고 있습니다

onSubscribe onNext* (onError | onComplete)?

이 텍스트가 위에 그림으로 설명이 됩니다. (혹시 api문서 읽으시다가 이런게 나오면 저런 flow라고 생각하면 됩니다) 두번째로 Publisher는 Obsavable입니다. Observer패턴에서는 Observer들이 Observable에 자신을 등록하는데 여기서는 Observer의 역활을 하는 Subscriber가 Publisher에 등록을 합니다

public interface Publisher {
    public void subscribe(Subscriber<? super T> s);
}

Subscriber는 Publisher의 subscribe메소드를 통해 등록을 합니다.(Observable에서는 addObservers) 세번째로 Subscription입니다. Subscription의 개념이 가장 중요한거 같은데 얘는 Publisher와 Subscriber 사에에서 역활을 하고 있습니다.

public interface Subscription {
    public void request(long n);
    public void cancel();
}

우선 Api부터 보면 request는 long타입의 파라미터를 받고 있는데 Subscriber가 이 메소드를 통해 요청을 하게 됩니다. 만약 데이터 3개가 필요하면 long파라미터에 3을 던져주면 Subscription은 3개를 던져주게 되죠. 만약 데이터가 10개정도가 있다고 하고 Subscriber가 한번에 3개씩 처리할수 있을때 request를 4번 호출하면 데이터가 3개->3개->3개->1개를 보내주는 것을 하게 되겠죠. 이때 request는 subscribe의 onNext를 통해 3개의 데이터를 전달합니다. Subscriber와 Subscription은 요청자와 응답자의 관계가 되겠군요. cancel은 메소드명 그대로 취소할때 사용됩니다. 만약 thread의 위에서 진행되고 있다면 cancel메소드의 일이 많아지겠군요. 스펙에서도 must be thread-safe라고 얘기하고 있습니다. cancel이 된다면 request는 멈춰야 겠죠. 4번째인 Processer는 Subscriber와 Publisher가 합쳐진 아이이기 때문에 설명은 생략합니다. 대충 스펙을 통해 Reactive Streams 표준을 확인했지만 실제 구현된 내용을 확인해봐야죠. Reactive Streams예제Reactive Streams가 제공해주는 예제는 개인적으로 봤을 때 정말 예술입니다만, 개념 자체를 이해하기에는 많은 양이 코드와 기능이 들어가 있습니다. 저는 심플하게 모든 스펙을 구현하지 않고 데이터 흐름만을 볼수 있도록 작성해봤습니다[https://github.com/devload/RxJavaExample/blob/master/src/main/java/ReactiveStreamSample.java](https://github.com/devload/RxJavaExample/blob/master/src/main/java/ReactiveStreamSample.java) ![](http://ahea.files.wordpress.com/2017/02/af99a7001b5889a906918a49ad6a5dc4.png) ![](http://ahea.files.wordpress.com/2017/02/05e5639d9d19f8eba427d963be838e6b.png) 저의 샘플 같은 경우에는 Publisher와 Subscription을 같이 구현했습니다. publisher의 subscribe메소드에는 1부터 10까지 가지고 있는 stack이 있습니다. subscriber의 onSubscription을 호출하게 되는데 이부분은 api spec에서 최초로 한번 호출해야 하는 부분이고 여기서는 inner클래스로 넣었습니다. cancel은 구현하지 않았으며 request를 보면 요청할 갯수를 파라미터로 받고 요청한 l개 많큼 스택에서 꺼내서 subscriber의 onNext를 통해 전달합니다. 만약 비어 있으면 onComplete를 호출하고요.즉 1부터 10까지 보내주는 Subscription입니다. ![](http://ahea.files.wordpress.com/2017/02/7168660c3be86ea52235868b33935e02.png) Subscriber는 onSubscribe, onNext, onError, onComplete를 구현하는데 대부분 가져온 데이터를 찍어보는 식으로 했습니다.여기서 주목할 것은 onSubscribe에서 subscriber에게 request를 날리는데, 1을 날렸으면 Subscription에서는 1개씩 onNext에게 데이터를 보내게 됩니다. onNext를 통해 데이터를 받아오면 해당 데이터를 처리하고 다시 request를 날립니다.마지막 줄에는 밖에서 publisher에 subscriber를 등록하는 코드가 깨알같이 캡쳐에 포함되었네요실행하면 다음과 같이 나옵니다 우선 단순하게 모든 스펙을 구현하지는 않았지만 api기준에 맞게 흐름을 태워봤습니다. 단순히 이것이 어떻게 우리의 개발하는 부분에서 적용이 될지는 아직 모르겠습니다. 하지만 우리는 개발자이기 때문에 단순한 이런 예제를 가지고 봤지만 다양한 스펙과 환경을 상상해볼수 있습니다. 만약 Subscription에서 줄 데이터가 DB에서 가져오는 데이터일경우 지금처럼 스택에서 꺼내오는 코드가 아닌 다른 jdbc코드가 될수 있겠죠, 또 만약 Subscriber에서 단순 System.out.println이 아닌 FileOutput이 될수도 있고 다른 네트워크로 http Rest방식으로 데이터를 쏠수도 있고요, 또~ 만약 Subscriber에서 onSubscribe가 최초 호출되었을때 ThreadPool을 10개를 세팅하고 request(10)을 해서 onNext에서는 데이터 하나당 스레드를 하나씩 줘서 처리를 하게 만들수도 있고요… 또~~ 만약에 Publisher에 등록된 Subscriber의 타입에 따라 비즈니스에 맞게 Subscription을 종류별로 만들어서 줄수도 있고요~~ 또~~~ 만약에 Publisher와 Subscription은 프레임워크에서 이미 구현이 되어 있다면? 우리는 그 구조에 맞게 Subscriber를 구현해주면 되겠죠 이렇게 생각하면 끝이 없겠네요..

정리
==

Reactive Stream에 대해 간략히 알아봤습니다. Reactive Stream을 이용하면 엄청난 소프트웨어를 만들수 있고, 난 심플한 코드 몇줄로 어썸~한 코드를 만들어 낼수 있을꺼야 라고 생각하셨다면, 조금더 Reactive Streams를 이해하셔야 겠습니다. 제가 여기서 소개한 Reactive Streams는 표준을 위한 api 스펙이며 구현체는 제각각입니다. 여러 구현체들이 있을수 있겠지만 이중 RxJava라는 것은 어떤지 다음에 보겠습니다.