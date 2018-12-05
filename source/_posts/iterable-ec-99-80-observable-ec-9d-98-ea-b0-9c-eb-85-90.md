---
title: Iterable와 Observable의 개념
tags:
  - design pattern
  - java
  - pattern
  - reactive
url: 134.html
id: 134
date: 2017-02-02 17:53:44
---

Reactive 글 모음

1.  [Iterable와 Observable의 개념](https://ahea.wordpress.com/2017/02/02/iterable%ec%99%80-observable%ec%9d%98-%ea%b0%9c%eb%85%90/)
2.  [Reactive History](https://ahea.wordpress.com/2017/02/03/reactive-history/)
3.  [Reactive Streams](https://ahea.wordpress.com/2017/02/13/reactive-streams/)
4.  [RxJava](https://ahea.wordpress.com/2017/02/13/rxjava/)
5.  [Spring Reactive](https://ahea.wordpress.com/2017/02/15/spring-reactive/)
6.  [Reactive PublishOn, SubscribeOn](https://ahea.wordpress.com/2017/02/20/reactive-publishon-subscribeon/)

* * *

java에서는 데이터의 연속적인 자료구조를 표현할때 List(Collection)를 사용합니다.

![](https://ahea.files.wordpress.com/2017/02/134_fix5844lwm.png)

그리고 이런 반복적인 데이터를 꺼내오기 위해 for-each문을 자주 사용하고 있습니다

![](https://ahea.files.wordpress.com/2017/02/134_jabiyhlyuh.png)

이것이 가능한 이유는 List가 Iterable이라는 interface를 구현하고 있기 때문입니다

![](https://ahea.files.wordpress.com/2017/02/134_qg5fbqurl5.png)

인텔리제이를 통해 Iterable을 열어봤습니다

![](https://ahea.files.wordpress.com/2017/02/134_ivksqohpoo.png)

34-35 라인을 보면 흥미롭게 이 인터페이스를 구현하면 for-each loop문에 사용할수 있다고 합니다.

51 라인을 보면 Iterator라는것이 있어 이것을 이용하여 데이터를 꺼내올수 있습니다

Iterator는 다음과 같은 메소드가 정의되어 있는데

![](https://ahea.files.wordpress.com/2017/02/134_nd8zjqjutt.png)

이중 핵심은 hasNext()를 통해 가져올 값이 있는지 체크하고 next()를 통해 가져오는 메소드입니다

이 내용들을 토대로 위에  dataArr를 List타입에서 Iterable타입으로 바꾸고 for-each문을 Iterator를 통해 돌리는 코드를 작성한다면 다음과 같이 되겠습니다

![](https://ahea.files.wordpress.com/2017/02/134_tjpnfeibp7.png)

**_정리. Iterable은 Iterator를 통해 데이터를 꺼내오고, Iterator의 next()를 통해 데이터를 가져온다_**

* * *

이번에는 Observable을 살펴봅시다

자바에서 Observable을 다음과 같이 정의해주고 있습니다

![](https://ahea.files.wordpress.com/2017/02/134_ndq2yvp7ua.png)

영어가 짧아 멋들어진 해석은 안되지만 중간에 키워드를 보면 모델-뷰 패러다임이라는 것도 보이고요, Observable은 하나 또는 여러개의 Observer를 가진다고 하네요, Observable은 notifyObservers메소드를 호출함으로 변화를 Observer들에게 알려준다고 합니다

이정도만 알고 코드를 작성해봅시다

우리는 Observable을 상속받은 클래스를 하나 만듭니다

![](https://ahea.files.wordpress.com/2017/02/134_ojkvegg9tk.png)

우리가 만든 Observable은 say라는 메소드를 가지며 say가 호출될경우 setChanged()를 통해 변화를 알려주고 notifyObservers()를 통해 등록된 Observer들에게 데이터를 날려줍니다

실제로 확인할수 있도록 우리가 만든 Observable에 Observer들을 등록해봅시다

![](https://ahea.files.wordpress.com/2017/02/134_g_clco7re_.png)

Observer1을 만들어서 addObserver를 통해 등록하고 우리가 만든 Observable에 만든 say()를 호출합니다

실행결과를 보면

![](https://ahea.files.wordpress.com/2017/02/134_f2cdjsbayp.png)

가 나옵니다

실행과정을 다시한번 살펴 보면

say()가 호출되었을 때 notifyObservers()를 통해 미리 등록된 Observer들에게 값을 전달합니다

**_정리 Observable은 등록된 Observer들에게 notifyObservers()를 통해 데이터를 보낸다._**

두가지 방식 모두 같은 기능을 하고 있습니다

하지만 처리과정에서 차이를 보이고 있습니다

event

Iterable(pull)

Observable(push)

retrieve data

Tnext( void )

voidnotifyObservers( T )

complete check

hasNext

setChanged

이 표에서 주목할점은 Iterable은 pull, Observable은 push라고 정리되어 있습니다.

pull방식은 데이터를 땡겨오는것이죠, Iterator.next()를 하면 데이터를 리턴받는 부분을 pull방식으로 봅니다.

반대로 push방식은 데이터를 던져주죠, notifyObservers()에 데이터를 파라미터로 줌으로서 등록된 옵저버들에게 데이터를 push합니다.

Iterable < — > Observable

Pull < — > Push

두개의 차이점을 이해하셨나요?

차이가 분명 있으면서도 같은 기능을 하고 있습니다. 이부분을 duality라고 부릅니다.

duality란

**A와 B가 있을 때 A에서 성립하는 정리를 뒤집어서 B에도 적용할 수 있는 경우**를 말한다. 한 마디로 A와 B의 본질이 같다는 뜻이다.

(by http://huns.me/development/2051)

duality라는 단어를 설명하기 참 어렵군요..

여튼 이중 Observable은 ReactiveX의 개념에서 가장 기초가 되는 개념입니다.

이번 포스팅에서는 Iterable과 Observable을 비교하며 배웠습니다

-추가-

코드를 java8에 맞게 다이어트 시킵시다

![](https://ahea.files.wordpress.com/2017/02/134_lmegcbiqbt.png)

이 코드를 다음과 같이 한줄로 하면 더 깔끔하겠군요

![](https://ahea.files.wordpress.com/2017/02/134_bh_ylzbinn.png)

-전체코드-

IteratorMain.java

importjava.util.Arrays; import java.util.Iterator;

publicclassIterableMain { publicstaticvoidmain(String\[\] args) { Iterable dataArr = Arrays.asList(1,2,3,4); for(Iterator it = dataArr.iterator();it.hasNext();) { System.out.println(it.next()); } } }

ObservableMain.java

importjava.util.Arrays; importjava.util.List; importjava.util.Observable; import java.util.Observer;

publicclassObservableMain { publicstaticvoidmain(String\[\] args) { Observable1 observable = new Observable1(); Observer observer1 = (o,data) -> System.out.println(data); observable.addObserver(observer1); observable.say(); } } classObservable1extendsObservable { publicvoidsay() { List dataArr = Arrays.asList(1,2,3,4,5); for(Object i : dataArr) { setChanged(); notifyObservers(i); } } }