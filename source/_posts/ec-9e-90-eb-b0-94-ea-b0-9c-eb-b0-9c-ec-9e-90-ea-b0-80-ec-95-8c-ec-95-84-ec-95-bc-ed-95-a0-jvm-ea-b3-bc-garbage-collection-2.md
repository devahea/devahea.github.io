---
title: 자바개발자가 알아야 할 jvm과 garbage collection – 2
url: 1406.html
id: 1406
categories:
  - java
date: 2017-07-26 10:35:34
tags:
---

이전글 : https://ahea.wordpress.com/2017/05/25/자바개발자가-알아야-할-jvm과-garbage-collection/ 자바의 메모리 구조를 살펴보자면 다음과 같습니다. ![439980E1-FE4F-4557-9EB0-31B2B6BEAD88.png](https://ahea.files.wordpress.com/2017/07/439980e1-fe4f-4557-9eb0-31b2b6bead88.png) heap 메모리는 young 영역과 old 영역으로 구분 되고 Young 영역은 3개의 영역으로 나뉜다. - Eden 영역, Survivor 영역 2개 perm gen은  아래와 같은 정보가 포함되있다. https://yckwon2nd.blogspot.kr/2014/04/garbage-collection.html 여기 참조.

1\. Class 의 Meta정보 (pkg path 정보라고 보면 됨, text 정보)

2\. Method의  Meta 정보

3\. Static Object

4\. 상수화된 String Object

5\. Class와 관련된 배열 객체 Meta 정보

6\. JVM 내부적인 객체들과 최적화컴파일러(JIT)의 최적화 정보

perm gen 설명

The permanent generation is special because it holds meta-data describing user classes (classes that are not part of the Java language). Examples of such meta-data are objects describing classes and methods and they are stored in the Permanent Generation. Applications with large code-base can quickly fill up this segment of the heap which will causejava.lang.OutOfMemoryError: PermGen no matter how high your -Xmx and how much memory you have on the machine.

프로젝트가 커지면 perm gen이 커서 에러가 나는데 이떄는 MaxPermSize를 jvm 옵션에 줘서 크게 하면 해결된다. java8에서는 고질적인 perm gen space error를 해결하기 위해서 perm 영역을 없애버렸다 그래서  -XX:MaxPermSize 설정이 사라지고 -XX:MaxMetaspaceSize 로 바뀌게 되었다. 각각 jvm default 값도 있는데 귀찬아서 패스. ![2](https://ahea.files.wordpress.com/2017/07/2.png)![3](https://ahea.files.wordpress.com/2017/07/3.png)![4](https://ahea.files.wordpress.com/2017/07/4.png)![5](https://ahea.files.wordpress.com/2017/07/5.png) 이 사진은 오라클 홈페이지에 나와있는 사진인데, 설명은 naver d2에 있는게 잘 되있어서 참고했다. 새로 생성한 객체는 Eden에 생성된다. Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.(age +1 되고 minor gc 발생) Eden 영역에서 GC가 발생하면 이미 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓인다.(객체가 나이를 계속 먹어감) 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다. 그리고 가득 찬 Survivor 영역은 아무 데이터도 없는 상태로 된다. 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 Old 영역으로 이동하게 된다. old영역이 가득 차면 full gc 발생(major gc) Old - mark sweep compact **알고리즘** Old 영역의 GC는 mark-sweep-compact 알고리즘 사용한다. 알고리즘의 첫 단계는 Old 영역에 살아 있는 객체를 식별(Mark) 힙(heap)의 앞 부분부터 확인하여 살아 있는 것만 남김 (Sweep) 각 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눈다(Compaction) 논외로 gc가 발생하게 되면 stop the world(stw)라고 해서 gc가 메모리 정리를 위해서 어플리케이션 실행을 중지 시키는데 gc를 돌리는 thread 만 빼고 모든 thread 를 정지 시킨다. 만약에 gc를 튜닝한다면 stw의 시간을 줄이는것을 목표로 한다. 말로 ppt를 보며 설명하면 쉬운데 글로 막상 글로 정리할려고 하니 잘 정리가 안된다...