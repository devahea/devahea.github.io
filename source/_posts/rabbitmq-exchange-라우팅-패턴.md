---
title: rabbitmq exchange 라우팅 패턴
date: 2019-04-30 19:30:38
tags: 
  - Message Queue
  - AMQP
  - RabbitMQ
  - Exchange
  - Routing
  - patten
  
img: /images\amqp\3-1.png
author: 최경운
---


### Exchange 라우팅 패턴

-   RabbitMQ 최대 강점은 애플리케이션이 제공한 라우팅 정보를 기반으로 서로 다른 큐로 유연하게 라우팅 가능
    
-   RabbitMQ의 4가지 기본 유형의 익스체인지와 유형을 알아본다.
    
-   Direct Exchange
    
-   Fanout Exchange
    
-   Topic Exchange
    
-   Headers Exchange
    

### Direct Exchange

-   다이렉트 익스체인지는 라우팅 키를 기반으로 메시지를 큐에 전달한다.
-   특정 큐에 메시지를 하나씩 전달한다.
-   RabbitMq에 기본으로 내장돼 있어서 추가 플러그인이 필요하지 않다.(매우 단순함)

direct exchange 는 unicast routing 방식에 적합하다.. 여기서 unicast routing 이란....

> 고유 주소로 식별된 하나의 네트워크 목적지에 메시지를 전송하는 방식  
> 참고 : [https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%88%EC%BA%90%EC%8A%A4%ED%8A%B8](https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%88%EC%BA%90%EC%8A%A4%ED%8A%B8)

1.  큐와 익스체인지가 routing key K를 이용해서 바인딩됨.
2.  이때 새로운 메시지와 routing key R 이 도착하면 exchange는 K = R일 경우 큐로 라우팅 한다.

Direct exchanges는 다양한 소비자에게 일을 분배 하기 위해 사용된다.  
**중요!!! AMQP 0-9-1, 메시지가 queue가 아닌 cunsumer들 사이에서 로드 밸런싱 된다는 것.**

![](/images\amqp\3-1.png)

### Fanout Exchange

-   다이렉트 익스체인지와 달리 전체 큐를 대상으로 처리한다.
-   Fanout Exchange로 발행된 메시지는 Fanout Exchange에 연결된 모든 큐에 전달된다.
-   라우팅 키를 평가할 필요가 없기 때문에 상당한 성능적인 이점이 있다.

> 스포츠 뉴스 사이트에서 모바일 클라이언트에게 실시간 점수를 업데이트할 수 있다.  
> 글로벌 이벤트에 활용될 수 있다.

![](/images\amqp\3-2.png)

### Topic Exchange

-   Topic Exchange는 하나 이상의 큐에게 Routing key 패턴 기반으로 Queue에 binding 한다
-   publish/subscribe pattern에 사용되거나 다양한 범위에서 사용된다.
-   주로 multicast routing에 사용된다.

예를 들어 image.new.profile, image.new.gallery, image.delete.profile이라는 라우팅 키가 있을때  
image.new.# : image.new.profile, image.new.gallery

#.profile : image.new.profile, image.delete.profile  
위와 같이 Routing key의 특정 파트에 맞게 메시지를 Routing 할 수 있다.

-   패턴 매칭을 사용하는 대신 전체 라우팅 키로 큐를 연결하면 된다.
-   #을 라우팅 키로 큐에 연결하면 Fanout exchange와 같은 효과를 낼 수 있다.

### Header Exchange

-   다양한 속성에서 라우팅 키보다 쉽게 사용하도록 디자인되었다.
-   메시지 속성 중 headers 테이블을 사용해 특정한 규칙의 라우팅을 처리한다.
-   Queue.Bind 메소드의 인수로 x-match를 사용한다.
    -   x-match = any 일 경우 헤더 테이블 값 중 하나가 연결된 값 중 하나와 일치하면 메시지 전달
    -   x-match = all 일 경우 모든 값이 일치해야 메시지를 전달한다.

출처 : [https://www.rabbitmq.com/tutorials/amqp-concepts.html](https://www.rabbitmq.com/tutorials/amqp-concepts.html)
