---
title: 선택이 아닌 필수 TDD
url: 1759.html
id: 1759
categories:
  - 미분류
date: 2018-09-10 09:32:02
tags:
---

### Test-First Development

TDD = TFD(Test-First Development) + Refactoring

TDD는 TFD와 리팩토링이 합쳐진 개발 방법론으로 소프트웨어 엔지니어인 켄트 벡(KentBeck)에 의해 고안된 실제 코드를 작성하기 전 테스트 코드를 먼저 작성하여 개발하는 SW 개발 방법론이다.

> What is the primary goal of TDD? One view is the goal of TDD is specification and not validation (Martin, Newkirk, and Kess 2003). In other words, it’s one way to think through your requirements or design before your write your functional code (implying that TDD is both an important agile requirements and agile design technique). Another view is that TDD is a programming technique. As Ron Jeffries likes to say, the goal of TDD is to write clean code that works. - agiledata

TDD는 디자인, 기술적 관점에 따라 기본적인 목표는 다르다. 첫 번째로 디자인 관점으로는 검증이 아닌 명세를 목표로 하고 있다. 즉 TDD 자체가 요구사항이고 디자인으로 생각한다. 또 다른 기술적인 관점에서의 TDD의 목표는 작동하는 깨끗한 코드(`Clean code that works(중복이 없고 명확한 코드)`)를 작성하는 것이다. TDD는 일반적인 개발 프로세스의 고질적인 문제점들을 고안해서 나온 개발 방법론이다. 그 때문에 TDD와 일반 개발 프로세스는 차이를 보면 우리가 왜 TDD를 해야 하는지 알 수 있다.

* * *

### 일반적인 개발 프로세스의 문제점들

  ![](https://ahea.files.wordpress.com/2018/09/tdd1.png?w=300)

일반적인 개발 프로세스

일반적인 개발 프로세스는 `설계(디자인) &gt; 개발(코드 작성) &gt; 테스트`하는 단계를 반복한다. 실제 실무에서 개발자 대부분이 `일반적인 개발 프로세스`를 따라가고 있다. 이러한 개발 방법론에 익숙해진 개발자가 보기엔 고질적인 문제점을 찾아 볼 수 없다. 어쩌면 개발에 나타나는 문제점들이 당연하다고 여기고 있을 수 있다. 하지만 `일반적인 개발 프로세스`에서의 문제점은 크게 세 가지로 나타난다.

> *   자체 버그 검출 능력 저하
> *   소스코드의 품질 저하
> *   자체 테스트 비용의 증가

이 문제점들이 발생되는 이유는 간단하다. 어느 프로젝트든 초기 설계가 완벽하다고 말할 수 없기 때문이다. 고객의 요구사항 또는 디자인의 오류 등 많은 외부 또는 내부 조건에 의해 재설계하여 점직전으로 완벽한 설계로 나아간다. 재설계로 인해 개발자는 코드를 삽입, 수정, 삭제하는 과정에서 쓰레기 코드가 남거나 코드가 중복처리 될 가능성이 크다. 결론적으로 이러한 코드들은 재사용이 어렵다. 더 나아가 코드가 복잡해지고 관리가 어려워져 유지보수를 어렵게 만든다. 이러한 코드는 작은 부분의 기능 수정에도 모든 부분을 테스트해야 하므로 전체적인 버그를 검출하기 어려움이 따라 `자체 버그 검출 능력`이 저하된다. 그 결과 어디서 버그가 발생할지 모르기 때문에 잘못된 코드도 고치지 않으려 하는 현상이 나타나고 이러한 현상은 `소스코드의 품질 저하`에 직결된다. 작은 수정에도 모든 기능을 다시 테스트해야 하는 문제가 발생하여 `자체 테스트 비용`이 증가 된다.

### 두려움을 관리하는 TDD

> Test-driven development (TDD) is a way of managing fear during programming. - Kent Beak

  켄트 벡은 자신의 글에 'TDD는 개발에 있어 두려움을 관리해준다.'라고 명시했다. 여기서의 두려움이란 `일반적인 개발 프로세스`에서 나타난 여러 문제점을 뜻한다. 이러한 문제점들은 중복된 코드로부터 시작되는데  `Clean code that works`를 추가하는 TDD는 이러한 중복된 코드로부터 발생하는 많은 문제점을 해결해준다. 무엇보다 TDD와 `일반적인 개발 프로세스`의 가장 큰 차이점은 테스트 코드를 작성한 뒤에 코드를 작성한다는 점이다. ![](https://ahea.files.wordpress.com/2018/09/tdd2.png?w=300)

TDD 개발 프로세스

TDD의 개발 프로세스는 다음과 같다. `디자인(설계)` 단계에서 프로그래밍 목적을 반드시 미리 정의해야만 하고 또 무엇을 테스트해야 할지 미리 정의(테스트 케이스 작성)해야만 한다. ![](https://ahea.files.wordpress.com/2018/09/architecturetfd.png?w=284)

TFD 순서도

`테스트 케이스`가 작성된 후 `테스트 코드 작성` 단계에서 `TFD 개발 프로세스`와 같은 방식으로 테스트 코드를 작성한다. 테스트 코드를 작성하는 도중에 발생하는 예외 사항(버그, 수정사항)들은 `테스트 케이스`에 추가하고 설계를 개선한다. 이후 테스트가 통과된 코드만을 `코드 작성` 단계에서 실제 코드를 작성한다. 이러한 반복적인 단계가 진행되면서 자연스럽게 코드의 버그는 줄어들고, 소스코드는 간결해진다. 또한, `테스트 케이스` 작성으로 인해 자연스럽게 설계가 개선됨으로 재설계 시간이 절감된다.

* * *

 

### Clean Code

TDD의 목표는 작동하는 깨끗한 코드(`Clean code that works`)를 작성하는 것이다. 하지만 아직도 'TDD는 어떻길래 중복코드가 없어지는 마술을 보이는 걸까?'는 의문이 생긴다.

> *   Don’t write a line of new code unless you first have a failing automated.
> *   Eliminate duplication. - KentBeck

켄트 벡은 이에 `Clean code that works`에 대한 TDD를 두 가지 과정으로 정의했다. 처음 실패한 자동화 코드가 없으면 새로운 코드 행을 작성하지 않는다. 그다음 중복을 제거한다고 정의했지만 `Clean code that works`가 되어가는 과정이 명확하지 않다. TDD를 숙련한 사람이라면 켄트 벡의 정의에 대해 이해를 할 수 있지만, TDD의 입문자가 보기엔 켄트 벡의 정의는 너무나도 추상적이고 함축적이다. 여기서 TTD의 순서도를 보면 켄트 벡이 정의한 두 가지 원칙과 더불어 Clean Code가 되어가는 과정을 명확히 알 수 있다.   ![](https://ahea.files.wordpress.com/2018/09/architecturetdd.png?w=300)

TDD 순서도

1) 하나의 작은 단위의 테스트 코드를 작성한다. 2) 테스트를 한다. 3) 테스트가 통과될 때까지 테스트 코드를 수정한다. 4) 테스트가 통과된 테스트 코드는 리펙토링을 한다. 5) 리펙토링한 코드를 테스트한다. 6) 리펙토링한 코드가 통과 될때까지 코드를 수정한다. 7) 테스트가 통과되면 테스트 케이스에 있는 다음 테스트 코드를 작성한다. 이처럼 TDD는 작은 단위부터 시작하여 점진적으로 개발한다. 이때 일련의 반복적인 과정을 통해 자연스럽게 코드는 명확해지고 깔끔해진다. 여기서 우리는 한가지 규칙적인 반복 패턴을 볼 수 있다.

* * *

### 밥 삼촌의 TDD Cycle

클린 코드 책의 저자로 유명한 로버트 C 마틴(엉클 밥)은 TDD의 개발 프로세스를 따라가다 보면 발생하는 반복적인 패턴에 대해 시간별로 주기를 정의했다.

> I sat with KentBeck in 1999 and paired with him in order to learn.  What he taught me to do was certainly test first; but it involved a more fine-grained process than I’d ever seen before. He would write one line of a failing test, and then write the corresponding line of production code to make it pass. Sometimes it was slightly more than one line; but the scale he used was was very close to line by line. - Robert C. Martin(Uncle Bob)

위의 글을 보면 켄트 벡은 행(line) 단위의 테스트를 진행을 지향하여 개발한다는 걸 알 수 있다. 행 단위의 테스트는 밥이 제시한 세 가지 규칙에 따라 진행된다.

#### TDD의 세 가지 규칙

밥이 제시한 TDD의 세 가지의 규칙은 다음과 같다.

1.  먼저 실패하는 테스트 코드를 작성한다.
2.  컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 테스트 코드를 작성한다.
3.  현재 실패하는 테스트 코드가 통과된 코드만 실제 코드에 작성한다.

이 세 가지 규칙은 나노 주기라 불린다. 그 이유인즉슨 코드의 행 단위의 개발 및 테스트가 이루어지게 된다. 이때 초 단위의 반복적인 테스트 주기가 발생하게 되는데 이 주기를 TDD의 나노 주기라 불린다.  켄트 벡과 밥은 테스트 코드 작성에 있어 다양한 스타일이 있지만, 위와 같은 세 가지 규칙을 벗어난 개발 스타일을 금기시하고 있다.

#### RGR Cycle

> If we pull back to the minute by minute scale we see the micro-cycle that experienced TDDers follow. The Red/Green/Refactor cycle. - Robert C. Martin(Uncle Bob)

초 단위에 발생되는 나노 주기가 있다면 분 단위에 발생되는 RGR 주기가 있다. ![](https://ahea.files.wordpress.com/2018/09/tdd4.png?w=300)

RGR Cycle

RGR 주기는 단위 테스트(Unit Test)마다 발생된다. RGR 주기의 규칙은 다음과 같다.

1.  Red           실패하는 단위 테스트 만들기
2.  Grean       테스트가 통과하도록 작성하기
3.  Refactor   통과된 테스트 코드를 리팩토링하기

> Write a test, make it run, make it right. To make it run, one is allowed to violate principles of good design. Making it right means to refactor it. - KentBeck

여기서 우리는 Red 단계에서 Green로 진행되는 과정을 살펴봐야된다. 이 과정에서 켄트 벡은 테스트가 통과하기 위해 금기(복붙, 설계를 무시한 개발)가 되는 행동을 해도 된다고 한다. 이때 발생하는 중복된 코드, 올바르지 못한 구조 등은 Refactor 단계에서 해결해준다고 말한다. 이 점은 로직에 따라서 `일반 개발 프로세스`보다 개발속도가 더 나올 수 있다는 점을 알 수 있다.

> Make It Work Make It Right Make It Fast - KentBeck

RGR 주기에 대한 아이디어는 켄트 벡의 빨리 작동하는 코드 개발(TDD 원초적인 공식)으로 부터 착안했다. 결국 그 원초적인 공식에 따라 RGR 주기는 소프트웨어가 제대로 작동하는 데에 목적이 있다. 또한 RGR 주기는 실제 개발 단계에서 개발과 동시에 리팩토링을 하기 위한 활동이다.

### 시간단위의 마일스톤

TDD의 마지막 주기는 이전의 주기들을 통해 `Clean Architecture`로 이끌어가는 주기이다. 이전의 주기들을 매시간 단위로 점검하는 과정을 통해 제어해야 한다. 이유인즉슨 나노 주기와 RGR 주기는 작은 단위에 초점을 맞춰 빠르게 진행되고 있어서 개발의 방향성을 잃을 수 있는 위험이 크기 때문이다. 그로 인해 이 주기에서는 테스트 케이스를 통해 개발에 대한 방향성 및 구조적인 상황을 점검하는 시간을 가져야 한다.

* * *

### Unit Test

앞서 TDD의 주기들은 어디까지나 TDD가 `Clean code that works`을 지향하고 있기 때문이다. `Clean code that works`를 실현하기 위해선 리펙토링 과정은 필연적이다. 리펙토링은 RGR 주기에서 단위 테스트 단위로 이루어지고 있는데 이 때문에 단위 테스트와 TDD는 밀접한 관계를 맺고 있다. 다만 주객이 바뀌면 안 된다. 이 점은 많은 개발자가 오해하는 부분인데 단위 테스트를 하는 이유는 TDD가 지향하는 `Clean code that works`을 실현하기 위한 일종의 방법이지 단위 테스트를 하기 위해 TDD를 한다는 건 말은 모순이기 때문이다.

> TDD                 설계 프로세스 단위 테스트   정밀한 테스트 케이스

  일반적으로 단위 테스트는 객체 또는 모듈의 함수(자바에서는 클래스)에 초점을 맞춘다. 테스트가 단일 기능에 한정되도록 함으로써 테스트는 간단하고 신속하게 이루어진다. 특히 단위 테스트는 코드를 변경해야 하는 경우 유용하다. 코드가 작동하는지 확인하는 단위 테스트가 있으면 코드를 안전하게 변경할 수 있고 실행 시 다른 부분에서 프로그램이 중단되지 않는다는 점을 확신할 수 있다.

#### 단위 테스트의 FIRST 규칙

다음과 같이 밥은 단위 테스트의 규칙을 정의했다. ![](https://ahea.files.wordpress.com/2018/09/tdd5.png?w=300)

단위 테스트의 F.I.R.S.T 규칙

 

*   단위 테스트는 빨라야 한다. \- Fast
    
*   단위 테스트는 독립적으로 작성한다. \- Independent
    
*   단위 테스트는 어느 환경에서든 반복 가능해야 한다. \- Repeatable
    
*   단위 테스트는 자체검증이 되어야 한다. \- Selef-Validating
    
*   단위 테스트는 실제 코드를 작성 전에 작성해야 한다. \- Timely
    

**Fast** 테스트는 빨라야 한다. 여기서 빠름의 기준은 밀리 초(ms)이다. 단위 테스트를 테스트하는 데 있어 실행 시간이 0.5 초 또는 0.25 초가 걸리는 테스트는 빠른 테스트가 아니다. 하나의 프로젝트에서 적게는 몇백 개에서 많게는 수천 개의 테스트를 할 수 있으므로 테스트의 실행 시간은 빨라야 한다. 만약 테스트가 느리다면 개발자는 테스트를 주저하게 되고 자주 검증하지 않은 소스코드는 그만큼 버그가 발생할 확률이 높아진다. **Independent** 테스트에 사용된 데이터들은 서로 의존하면 안 된다. 테스트에 필요한 데이터는 테스트 내부에서 독립적으로 사용해야 한다. 만약 데이터가 서로에게 의존하면 테스트 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지기 때문이다. 때론 데이터의 존재 여부를 찾는 테스트가 있는 경우엔 해당 데이터는 테스트 내부에서 생성되어야 하며 나중에 테스트에 영향을 미치지 않도록 제거해야 한다. **Repeatable** 테스트는 어느 환경에서든 반복적으로 테스트를 실행할 수 있어야 한다. 여기서 환경은 네트워크 나 데이터베이스에 의존하지 않는 환경을 뜻한다. 결론적으로 인터넷이 되든 안 되든 데이터베이스에 접속하든 안 하든 언제 어디서나 테스트를 할 수 있어야 한다. 환경에 의존하지 않는 테스트가 실패할 수 있는 유일한 이유는 오로지 테스트할 클래스 또는 메소드가 제대로 작동하지 않기 때문이다. **Selef-Validating** 테스트는 자체 검증이 되어야 한다. 테스트의 검증은 수작업이 아닌 자동화가 되어야 하는데 테스트가 실행될 때마다 메서드 출력이 올바른지를 확인하는 것은 개발자가 결정해서는 안 된다. 이 때문에 자바 환경에서는 테스트에 대한 검증을 지원하는 JUnit을 사용하여 테스트의 통과 여부를 결정한다. **Timely** \- 단위 테스트는 실제 코드를 작성하기 전에 작성해야된다. 이 규칙은 TDD를 수행하는 경우 반드시 따라야 하는 규칙이다. FIRST 규칙은 단위 테스트를 작성하는 데 좋은 기준이 된다. 좀 더 다양한 단위 테스트를 작성하는 방법은 밥의 Clean Code 책을 참고하자.

* * *

### 결론

TDD는 `Clean code that works`를 목표로 한다. TDD는 TDD Cycle이라는 반복적인 주기가 발생한다. `Clean code that works`의 실현은 TDD Cycle 중 RGR 주기의 리펙토링 통해 이루어진다. 이 과정에서 테스트 코드는 중복 제거가 이루어지는데 여기서 중복 제거는 단순한 코드의 중복 제거를 넘어서 반복적인 기능을 하나의 클래스(또는 인터페이스)로 묶는 과정을 뜻하기도 한다. 즉 구조가 재설계 되기도 하는데 이러한 결과에서 우리는 TDD가 코드뿐만이 아닌 `Clean Architecture`를 실현하는 개발 방법론으로 사용할 수 있다는 점을 알 수 있다. 또한, 반복적인 자동화된 검증 과정을 통해 개발자에게 코드에 대해 신뢰를 하게 해줄뿐더러 더 나아가 소프트웨어의 버그 발생률을 감소시키고 품질 향상에 도움을 준다.

> TDD를 도입한 소프트웨어는 약 15~35% 정도의 개발시간 증가, 결함율(버그)은 약 40~90% 정도 줄어들었다. - Microsoft, IBM

위와 같이 연구 자료들은 TDD가 선택이 아닌 필수라는 사실을 대변하고 있다. 자세한 TDD의 자세한 기대효과를 알고 싶다면 [On the Effectiveness of the Test-First Approach to Programming](https://www.researchgate.net/publication/3188484_On_the_effectiveness_of_the_test-first_approach_to_programming)을 참고하자.

**Test** **Refectoring** **Clean code that works**

**.** **.** **.**

**TDD Cycle**

**.** **.** **.**

**Clean Architecture**

**.** **.** **.**

**TDD**

* * *

### 참고

[https://www.eecs.yorku.ca/course\_archive/2003-04/W/3311/sectionM/case\_studies/money/KentBeck\_TDD\_byexample.pdf](https://www.eecs.yorku.ca/course_archive/2003-04/W/3311/sectionM/case_studies/money/KentBeck_TDD_byexample.pdf) [http://agiledata.org/essays/tdd.html](http://agiledata.org/essays/tdd.html) [https://www.microsoft.com/en-us/research/group/empirical-software-engineering-group-ese/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fgroups%2Fese%2Fnagappan_tdd.pdf](https://www.microsoft.com/en-us/research/group/empirical-software-engineering-group-ese/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fgroups%2Fese%2Fnagappan_tdd.pdf) [https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html) [http://blog.cleancoder.com/uncle-bob/2013/09/23/Test-first.html](http://blog.cleancoder.com/uncle-bob/2013/09/23/Test-first.html) [http://wiki.c2.com/?MakeItWorkMakeItRightMakeItFast](http://wiki.c2.com/?MakeItWorkMakeItRightMakeItFast) [http://agileinaflash.blogspot.com/2009/02/first.html](http://agileinaflash.blogspot.com/2009/02/first.html) [https://pragprog.com/titles/olag/agile-in-a-flash](https://pragprog.com/titles/olag/agile-in-a-flash) [https://codeutopia.net/blog/2015/03/01/unit-testing-tdd-and-bdd/](https://codeutopia.net/blog/2015/03/01/unit-testing-tdd-and-bdd/) [https://xebia.com/blog/tdd-not-unit-tests/](https://xebia.com/blog/tdd-not-unit-tests/) [https://builttoadapt.io/why-tdd-489fdcdda05e](https://builttoadapt.io/why-tdd-489fdcdda05e)