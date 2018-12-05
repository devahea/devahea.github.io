---
title: 자바개발자가 알아야 할 jvm과 garbage collection - 1
url: 1107.html
id: 1107
categories:
  - java
date: 2017-05-25 15:34:59
tags:
---

자바 바이트코드는 JRE 위에서 동작합니다.

![1.png](https://ahea.files.wordpress.com/2017/05/1.jpg)

JRE는 자바 API와 JVM으로 구성되며, JVM의 역할은 자바 애플리케이션을 클래스 로더(Class Loader)를 통해 읽어 들여서 자바 API와 함께 실행하는 것입니다.

이 JRE에서 가장 중요한 요소는 자바 바이트코드를 해석하고 실행하는 JVM(Java Virtual Machine)입니다.

\- jvm architecture -

![1.png](https://ahea.files.wordpress.com/2017/05/11.png)

Execution Engine은 Load된 Class의 ByteCode를 실행하는 역할.

 

JVM의 특징 중 하나는 가비지 컬렉션입니다.

*   가비지 컬렉션(garbage collection): 클래스 인스턴스는 사용자 코드에 의해 명시적으로 생성되고 가비지 컬렉션에 의해 자동으로 파괴된다.

C와 Java의 차이점 중 하나인 가바지 컬렉션은 사용자가 메모리에 대해 신경 쓸 필요없이 가비지 컬렉션이 자동으로 관리해주는 역할을 합니다.

gc는 댕글러 포인터 때문에  필요합니다.

a -> b -> c 위와 같은 참조관계의 객체가 있다고 했을때,

b가 삭제되면 유효한 객체 A는 댕글링 포인터를 가집니다. c를 차지하는 부분은 누수가 발생해서, c는 접근할수도 해제 할수도 없습니다.

가비지 컬렉션은 어떤 가설을 가지고 동작됩니다.

세대 컬럭터는 대부분의 객체는 일찍죽는다라는 가설 바탕으로 (weak gerenation hypothises 은 프로그래밍 패러다임이나 구현언어에 관계없이 인정됨..)

어른 객체들의 회수에 노력을 집중함으로 최소한의 노력으로 최대한의 수율을 얻고자 시도하고

세대 컬렉터는 객체들을 나이에 따라 세대로 분리하고 별개의 영역에 배치합니다.

새대 컬렉터도 가끔은 힙 전체를 수집해야합니다.

긴 수명의 객체를 반복적으로 처리하지 않기 때문에 처리량을 향상 시킵니다.

![2](https://ahea.files.wordpress.com/2017/05/21.png)

위에 그림은 오라클 홈페이지에 나와있는 그림이고, 그림은 객체의 수명에 대한 일반적인 분포인데 처음에 많은 객체가 할당되지만 'die young'한것을 확인 할 수 있습니다.

출처 : [http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/img\_text/jsgct\_dt\_003\_alc\_vs\_srvng.html](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/generations.html#sthref16)

그럼 이 가비지 컬렉션은 어떤 메모리를 자동으로 관리해줄까 하는 의문이 생길 수 있는데

동작 방식에 따라 매우 다양한 종류가 있지만 공통적으로 크게 다음 2가지 작업을 수행한다고 볼 수 있습니다.

1.  힙(heap) 내의 객체 중에서 가비지(garbage)를 찾아낸다.
2.  찾아낸 가비지를 처리해서 힙의 메모리를 회수한다.

힙 데이터를 알기 전 Runtime Data Area 부터 알아보자면 ![3.png](https://ahea.files.wordpress.com/2017/05/3.png)

런타임 데이터 크게 3가지로 분류되는데

1\. 스레드가 차지하는 영역

2\. 객체를 생성 및 보관하는 하나의 큰 힙

3\. 클래스 정보가 차지하는 영역인 메서드 영역

  이중에서도 힙을 가비지 컬럭터가 관리하게 됩니다. 다음글에서는 java의 메모리는 어떻게 구분되고 gc는 어떻게 일어나는지 알아보겟습니다. https://ahea.wordpress.com/2017/07/26/자바개발자가-알아야-할-jvm과-garbage-collection-2/ 참고 : http://d2.naver.com/helloworld/329631 http://d2.naver.com/helloworld/1329