---
title: 'Reactive PublishOn, SubscribeOn'
url: 399.html
id: 399
categories:
  - reactive
date: 2017-02-20 16:27:04
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

[![](https://ahea.files.wordpress.com/2017/02/993d6acff3e9bc2448266c19570a4a18.png)](http://ahea.files.wordpress.com/2017/02/993d6acff3e9bc2448266c19570a4a18.png) 다음과 같은 코드가 있습니다. Publisher, Subscriber 그리고 Subscription이 있습니다. 각자가 처리되는 곳에서 로그를 찍었고 로그는 Slf4j를 이용했습니다. 결과를 보면 다음과 같이 나옵니다. [![](https://ahea.files.wordpress.com/2017/02/c95e4162e7e91ab27a2ddbc6e013e552.png)](http://ahea.files.wordpress.com/2017/02/c95e4162e7e91ab27a2ddbc6e013e552.png) 우리가 코딩한 그대로 subscriber가 publisher에 등록을 하면 내부처리가 진행되고 exit로 끝나게 됩니다. 이때 subscriber와 publisher의 진행은 메인메소드에서 진행됩니다. 이렇게 되면 subscriber가 등록을 하면 publisher가 데이터를 넣어주고 처리할때까지 메인메소드를 붙잡고 있게 됩니다. 이게 맞는것일까요… 결국 우리가 원하는것은 비동기-동기화 처리, 즉 publisher에 subscriber를 등록하게 되면 자기들은 자기들대로 진행하고 계속 메인메소드가 진행되는것을 하고 싶은겁니다. Reactive Streams가 겨우 Observerable을 해주기 위한 건 아니지 않습니까… 결국 Reactive Streams 가 해주고 싶었던 궁극적인 목적은 병렬프로그래밍을 손쉽게 하기 위해 옵저버 모델을 가져간것이죠. 옵저버 자체가 핵심은 아니였습니다. 이번 포스팅에서는 간단하게 PublishOn과 SubscribeOn 을 통해 Reactive Stream에서 병렬처리를 어떻게 하는지 알아보겠습니다

publishOn, subscribeOn
======================

위에 이슈를 보면 publisher에 subscriber를 등록하면 메인스레드가 아닌 다른 스레드가 진행함으로써 메인은 계속 진행되도록 만들고 싶었습니다 우리는 publishOn을 이용하여 처리하는방법을 보도록 하겠습니다 결국 간단하게 처리한다면 위에 코드에 pub.subscribe(sub)이라는 코드를 스레드로 감싸주면 되겠죠 [![](https://ahea.files.wordpress.com/2017/02/a8d16b50c36e94e68a14f6f2160ddf61.png)](http://ahea.files.wordpress.com/2017/02/a8d16b50c36e94e68a14f6f2160ddf61.png) 하지만 매번 메인 메소드에 이런 코드가 직접 뜬다는것은 구조상 부담스러운 일입니다 이때 우리는 publishOn방식을 이용하여 해결해보려고 합니다. 이방식은 쓰레드를 위한 퍼블리셔로 한번 더 감싸서 이 퍼블리셔가 쓰레드를 열어주는 것입니다. 뭐래는지 하나도 모르겠죠, 코드로 보겠습니다. [![](https://ahea.files.wordpress.com/2017/02/621d24b61738e06dd535abacfbabd4bf.png)](http://ahea.files.wordpress.com/2017/02/621d24b61738e06dd535abacfbabd4bf.png) 이것은 퍼블리셔입니다, 원래라면 퍼블리셔 안에는 subscription을 만들고 request 메소드를 구현해서 ~~ 하는 코드들이 있었습니다. publishOn은 이곳에서 원래 기존에 퍼블리셔에세 자신이 가지고 있는 퍼블리셔를 쓰레드를 통해 전달해줍니다. 그러면 기존 퍼블리셔는 subscriber를 전달 받아서 프로세스를 진행하게 되는것이죠 ![](https://ahea.files.wordpress.com/2017/02/ac40781cb9be1f0586f2fbd98c656798.png) 결국 마지막에 pub.subscribe(sub)이였던 코드를 위와 같이 바꿔주면 pubOnPub이 동작하게 되겠죠 실제 실행결과를 보면 놀랍게도 다음과 같이 바뀌었습니다 [![](https://ahea.files.wordpress.com/2017/02/9f9387c68e705f55f7be4bfb58507f56.png)](http://ahea.files.wordpress.com/2017/02/9f9387c68e705f55f7be4bfb58507f56.png) 기존 메인메소드에서 모든것이 다 진행됬던것과 달리 스레드 하나가 올라와서 publisher와 subscriber의 로직을 처리하고 있습니다 더 놀라운것은 메인메소드 가장 마지막에 찍었던 exit로그가 가장 먼저 실행이 되었고 그 다음 subscriber로직이 타게 되었습니다 아마도 메인스레드는 exit를 찍고 끝났을것입니다. 하지만 subscriber를 처리하는 스레드는 메인메소드가 끝나도 계속 진행하고 있었을 겁니다. 이 안에서도 문제는 있습니다. subscription의 request와 subscriber의 onNext는 같은 메소드로 진행되고 있습니다 이것은 어떤 문제가 생기냐면 onNext에서 작업이 오래 걸릴경우 다음 데이터를 보내야 하는 subscription은 대기하게 됩니다. 보내줄 데이터는 많은데 받을 아이는 역량이 부족하여 빨리 처리 못하고 있는 상태인거죠 이때는 반대로 subscribeOn을 이용하여 문제를 해결할 수 있습니다 아까 publishOn은 쓰레드를 대신 담당하는 Publisher를 만들었습니다. subscribeOn도 마찬가지 입니다. 쓰레드를 담당하는 subscriber를 만들면 됩니다 [![](https://ahea.files.wordpress.com/2017/02/7a611e4bf4339723b177ef126ecd7e6b.png)](http://ahea.files.wordpress.com/2017/02/7a611e4bf4339723b177ef126ecd7e6b.png) subscribeOn 객체를 만들었습니다. 내용을 보면 각각 메소드 모두 스레드를 올려서 기존 subscriber에게 작업을 전달합니다 그리고 publisher에 등록을 subscribeOn객체를 줬습니다. 이렇게 하면 어떻게 진행이 될까요? publisher에 등록을 하면 subscribeOn의 onSubscribe가 실행이 될거고 스레드를 열어서 원래 동작해야 할 애를 호출해줍니다 마찬가지로 onNext는 request에서 데이터를 보내주면 subscribeOn이 받아서 스레드를 열어서 원래 동작해야 할 애의 onNext를 호출해줍니다. 파라미터를 그대로 전달하면서 말이죠 결과 화면을 보시죠. [![](https://ahea.files.wordpress.com/2017/02/eba4b3191f303cb4cc1ba30a946a103d.png)](http://ahea.files.wordpress.com/2017/02/eba4b3191f303cb4cc1ba30a946a103d.png) 실행결과를 보면 Subscriber에서 호출되는것들이 각각 메소드에서 동작하고 있음을 확인 할 수 있습니다 만약 onNext의 작업이 오래 걸려도 Subscription은 다음 데이터를 보내줄것입니다. 왜냐면 처음 onNext를 보낸 스레드는 이미 끝났고 아직 onNext를 처리하는 스레드는 새로 만들어진 스레드가 진행중이기 때문에 interrupt하고 있지 않죠. 이 두개를 같이 쓰는 방식을 보면서 publishOn, subscribeOn 의 간략한 설명은 마치겠습니다 [![](https://ahea.files.wordpress.com/2017/02/fe7fa0423560c12961de476a417c1989.png)](http://ahea.files.wordpress.com/2017/02/fe7fa0423560c12961de476a417c1989.png)

Spring에서 subscribeOn, publishOn
===============================

Spring에서는 아주 쉽게 api에서 지원해주고 있습니다 Flux에서 publishOn과 subscribeOn메소드를 주고 있습니다 [![](https://ahea.files.wordpress.com/2017/02/4656c530c29f9dd0ef32e896f5410cac.png)](http://ahea.files.wordpress.com/2017/02/4656c530c29f9dd0ef32e896f5410cac.png) range는 1부터 10까지 데이터를 가져가는것이고 밑에 publishOn 을 넣었습니다 Schedulers.newSingle 을 통해 스레드를 관리하게 됩니다. 밑에는 log를 출력해주는 log(), 그리고 밑에는 subscribeOn 을 사용하였습니다 마지막으로 subscribe에서는 출력을 해주고 있네요 실행결과를 보겠습니다 [![](https://ahea.files.wordpress.com/2017/02/b977f7f05eac99e90b37a2ac6e74f747.png)](http://ahea.files.wordpress.com/2017/02/b977f7f05eac99e90b37a2ac6e74f747.png) 디테일하게 찍혀있는 로그는 Flux.log()에서 나오는 로그입니다. 스레드 정보를 보기에 좋습니다. 메인메소드는 시작하자마자 로그 한줄 찍고 끝났고, publishOn이 진행됩니다 또 보면 sub-1과 pub-2가 두개가 띄어있습니다, 즉 request하는 스레드와 onNext하는 스레드가 다른 스레드로 동작하고 있음을 확인할수 있습니다

정리
==

이렇게 스프링에서 publishOn과 subscribeOn을 통해 Reactive를 병렬적으로 처리할수 있도록 하는 방법을 알았고 그것들이 어떻게 동작되는지 살펴봤습니다.