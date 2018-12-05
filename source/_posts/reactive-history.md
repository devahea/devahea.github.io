---
title: Reactive History
tags:
  - reactive
  - reactive extensions
  - reactiveX
  - rx
  - volta
url: 165.html
id: 165
date: 2017-02-03 11:39:27
---

Reactive 글 모음

1.  [Iterable와 Observable의 개념](https://ahea.wordpress.com/2017/02/02/iterable%ec%99%80-observable%ec%9d%98-%ea%b0%9c%eb%85%90/)
2.  [Reactive History](https://ahea.wordpress.com/2017/02/03/reactive-history/)
3.  [Reactive Streams](https://ahea.wordpress.com/2017/02/13/reactive-streams/)
4.  [RxJava](https://ahea.wordpress.com/2017/02/13/rxjava/)
5.  [Spring Reactive](https://ahea.wordpress.com/2017/02/15/spring-reactive/)
6.  [Reactive PublishOn, SubscribeOn](https://ahea.wordpress.com/2017/02/20/reactive-publishon-subscribeon/)

* * *

volta project와 ReactiveX
------------------------

![voltal-and-reactivex](https://ahea.files.wordpress.com/2017/02/voltal-and-reactivex.png)

이것은 volta와 reactiveX의 로고입니다.

비슷함을 넘어서서 똑같죠

해당 이유는 msdn포럼에서 Erik Meijer가 리플로 대답을 했습니다

"It is brought to you by the same team."

###### https://social.msdn.microsoft.com/Forums/en-US/c48eb793-30ba-4be1-a5b7-46d6efd815f5/volta-logo-vs-rx-logo?forum=rx

###### [https://channel9.msdn.com/blogs/charles/getting-started-with-rx-extensions-for-net](https://channel9.msdn.com/blogs/charles/getting-started-with-rx-extensions-for-net)

what is volta
-------------

volta는 ms의 제품연구 부문인 ms live labs에서 생긴 프로젝트이며 애플리케이션의 각 구성요소를 네트워크 내에서 분배하는 것을 지원할 목적으로 설계된 개발 툴입니다

개발자는 보통 비동기/동기 상태에서 어떤식으로 개발해야 최고의 포퍼먼스가 날지 고민하게 되는데 이런 부분을 쉽게 해결하기 위해 volta가 진행되었습니다.

alex daiey는 ‘비주얼 스튜디오 2008’의 애드인 소프트웨어이며 개발자는 클라이언트 측의 코드를 쓰고, 다음에 어느 코드를 어디서 실행시킬지 주석으로 지정할 수 있다고 설명하고 있습니다

###### [http://www.zdnet.com/article/microsoft-architect-compares-volta-and-googles-gwt/](http://www.zdnet.com/article/microsoft-architect-compares-volta-and-googles-gwt/)

volta와 많이 거론되는 것이 gwt(google web toolkit)인데 java를 javascript로 변환시키는 gwt에 비해, volta는 조금더 넓게 닷넷 프레임워크에 적용시킬 계획이였으나 불행하게도 volta는 중단됬습니다

volta에서 Reactive Extensions로
----------------------------

volta의 일부였던 지금은 reactive framework는 2009년 reactive extensions로 이름을 바꾸고 공식 릴리즈 됩니다

*   Reactive Extensions For .NET 3.5 Sp1
*   Reactive Extensions For .NET 4 beta 2
*   Rx for Silverlight3

reactive extensions라는 이름에 관한것은

###### [https://channel9.msdn.com/Shows/Going+Deep/E2E-Erik-Meijer-and-Wes-Dyer-Reactive-Framework-Rx-Under-the-Hood-1-of-2](https://channel9.msdn.com/Shows/Going+Deep/E2E-Erik-Meijer-and-Wes-Dyer-Reactive-Framework-Rx-Under-the-Hood-1-of-2)

에서 확인할수 있습니다

Reactive Extensions는 처음에는 오픈소스가 아니였으나 2012년 Eric Meijer는 rx.net rx.js, rx++ 를 오픈소스로 공개하게 됩니다

###### [http://www.hanselman.com/blog/ReactiveExtensionsRxIsNowOpenSource.aspx](http://www.hanselman.com/blog/ReactiveExtensionsRxIsNowOpenSource.aspx)

RxJava와 Netflix
---------------

Netflix의  TV User Interface 팀 개발자인 Jafar Husain은 rest service를 옵티마이징 하기 위해 아키텍쳐를 재설계 해야 한다고 생각했습니다

Netflix는 2012년 기준 북미의 인터넷 트래픽의 33%를 차지 할정도로 (당시 유튜브의 트래픽은 15%로 조사가 되었다) 엄청난 양의 데이터 처리를 요구받고 있었으며 Netflix는 엄청난 양의 네트워크의 포퍼먼스 문제를 해결하기 위한 옵티마이징 포인트를 Network Latency로 잡았습니다

넷플릭스는 사용자경험을 위해 api를 세분화 시키고 여러번 call이 들어오도록 설계되었습니다. 이안에서 network latency가 여러번 발생되여 지연되는 현상을 알게 되었습니다![request-multi_1252](https://ahea.files.wordpress.com/2017/02/request-multi_1252.png)

이런 문제를 해결하기 위해서는 네트워크 요청 단일화시켜서 축소시키는 것입니다. 즉 WAN의 대기시간을 줄이는 대신 처리가 빠른 강력한 하드웨어를 가진 서버를 둬서 이 문제를 해결하는 것입니다.

![request-single_1252](https://ahea.files.wordpress.com/2017/02/request-single_1252.png)

이때 고려했던 키포인트 중 하나가 Reactive programming model이였습니다.

동시성(concurrency)을 가져가면서 스레드의 안정성과 병렬처리를 개발하는것은 매우 복잡한 일이였습니다. 그래서 이부분을 추상화시킬 프레임워크가 필요했습니다.

특히 기존 클라이언트 코드를 만지지 않으면서 java API를 통해 비동기 처리가 되도록 만들어야 했습니다. 그래서 이부분을 비동기 콜백으로 처리하기 위한 함수형 프로그래밍을 사용하는 반응형 모델을 선택하게 되었습니다. 이부분을 Rx Observable 모델로 가게 된것입니다.

그래서 Jafar Husain은 jvm환경에서 ReactiveExtensions 모델이 동작되는 Java버전을 개발하게 되었으며 이것이 rxJava가 되었습니다

개발 패러다임의 변화와 맞물린 Reactive Extensions
------------------------------------

넷플릭스의 Reactive모델 적용은 단순히 포퍼먼스의 옵티마이징이 아니였다. Microservice architecture의 도입으로 비동기 통신에 기반하게 되었으며 이를 아주 쉽게 개발할수 있는 Reactive Programming 그중에서 대표적인 ReactiveExtensions가 주목받게 되었습니다.

웹 클라이언트에도 큰 변화가 있다. 그중에서도 javascript가 역동적으로 변하고 있다. 단순 돔을 제어하고 이벤트를 처리하는 언어였지만, 이제는 컴포넌트화 되고 다양한 프레임워크들이 쏟아져 나오고 있습니다. 서버의 변화로 클라이언트도 비동기처리에 대한 필요를 느끼고 reactiveExtensions의 rxJs가 많이 사용되고 있습니다. 최근 angular2에서도 react개 채택된것을 확인해본다면 이러한 패러다임의 변화가 아주 빠르게 바뀌고 있음을 알수 있습니다.

클라이언트 변화는 모바일에서도 마찬가지 입니다. SoundColud의 Matthias Käppler는 android에 reactiveExtensions를 적용한 rxAndroid를 내놓았으며 최근 rxSwift까지 나오면서 rxExtensions가 아주 빠르게 적용되고 바뀌어 나가고 있음을 알고 있습니다

Reactive and Java Future
------------------------

java의 reactive의 표준을 만들기 위해 Typesafe, Red Hat, Netflix, Pivotal, Oracle, Twitter, spray.io들이 모여 스펙을 정의하였습니다. 정확하게는 reactive stream에 대한 표준을 정의한것이며, Oracle은 이를 바탕으로 java9에서 해당 스펙이 어느정도 구현된 reactive stream이 탑재된다고 발표했습니다.

###### [http://www.reactive-streams.org/](http://www.reactive-streams.org/)

이와 맞물려 spring.io에서는 spring 5.x에서 reactive programming을 지원한다고 하는데요. java시장에서 큰 점유율을 가진 spring framework가 아주 빠르게 대응함에 따라 비동기 프로그래밍의 장점을 가졌던 node.js나 다른 언어들과 치열한 전쟁은 계속될거 같습니다. 하지만 기존 개발자들이 이러한 패러다임에 빠르게 변화할수 있는지는 아직 미지수입니다.