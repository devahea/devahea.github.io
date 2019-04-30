---
title: 'AMQ 모델과 Exchange, Queue, Binding 에 대해'
date: 2019-04-30 19:09:47
tags: 
  - Message Queue
  - AMQP
  - RabbitMQ
  - Exchange
  - binding
  - queue
  
img: /images\amqp\2-1.png
author: 최경운
---

### \* rabbitmq 강점과 유연성은 amqp 스펙에서 나온다. 

   - rabbitmq를 설명하기 앞서 amqp가 무엇인지 부터 알아보자.

     amqp (advanced message queuing protocol)

      - 메시징 프로토콜!!!!

      - amqp 스펙은 여러가지가 있는데 rabbitmq는 0-8, 0-9-1과 밀접하게 관련돼 있다.

      - rabbitmq는 amqp 스펙을 구현했는데, 주요 아케텍쳐와 통신 방식이 핵심이다. 

AMQP 스펙은 HTTP 프로토콜과 달리 네트워크 프로토콜의 정의뿐 아니라 서버 측 서비스와 동작 방식도 정의한다.

AMQ 모델은 메시지 라우팅 동작을 정의 하는 메시지 브로커의 **세 가지 추상 컴포넌트**를 다음과 같이 정의한다. 

\* 익스체인지 : 메시지 브로커에서 큐에 메시지를 전달하는 컴포넌트

\* 큐 : 메시지를 저장하는 디스크 상이나 메모리 상의 자료구조

\* 바인딩: 익스체인지에 전달된 메시지가 어떤 큐에 저장돼야 하는지 정의하는 컴포넌트 

여기서 이 3개 이해 못하고 넘어가면 두고두고 이해가 안간다.. 

꼭 이해하고 넘어가자.

![](/images\amqp\2-1.png)

1.  Producer가 exchange에게 메세지를 publish한다. (우체국으로 편지를 보내는거와 같다)
2.  exchange는 binding이라는 규칙을 사용해 메세지의 복사본을 Queue에 배포한다.  
    \-> (원문) Exchanges then distribute message copies to queues using rules called bindings.   
    \-> 실제 메시지를 큐에 넣는 대신 메시지에 대한 참조가 queue에 추가 된다. 메시지를 전달할 준비가 되면 큐는 이 참조를 사용해서 클라이언트에 전송한다. 메시지가 여러 목적지에 발행될 때, 참조만 저장해서 메모리를 적게 사용한다.  
    
3.  broker는 subscribe하고 있는 consumer에게 메시지를 전달하거나  
    요청시 queue에서 메시지를 fetch하거나 pull한다

#### \--- Exchange \---

\- amqp 모델이 정의하는 세 컴포넌트 중 하나. 

\- rabbitmq로 전송한 메시지를 수신하고 메시지를 보낼 위치를 결정한다.

\- 발행한 메시지가 처음 도착하는 지점, 메시지가 목적지에 도달할 수 있도록 라우팅 규칙 적용 담당.

\- 다양한 라우팅 규칙이 존재함(Direct exchange, Fanout exchange, Topic exchange, Headers exchange)

#### \---Queue ---

\- AMQP 스펙에 따라 큐 설정이 immutable 함.(선언 후 지정한 설정 변경 불가, 설정 변경시 삭제 후 다시 만들어야 함)

\- 수신한 메시지를 저장하는 역할을 한다. 메시지에 수행하는 작업을 정의하는 설정정보가 있다. 

queue 동작을 결정하는 다양한 설정이 존재 한다.

-   자동 삭제 큐  
    \-> comsumer와 연결을 맺고 메시지를 전달한 후 연결을 끓으면 큐 제거.
-   큐 독점 설정  
    \-> 설정하지 않으면 다수 consumer 가 큐를 구독 할 수 있음.  
    \-> 활성화 시 소비자가 연결 해제된 후 큐가 자동으로 제거 됨.  
    \-> 선언한 연결과 채널에서만 사용 할 수 있다. 
-   자동 메시지 만료  
    \-> 일정 기간 동안 사용하지 않은 큐를 삭제 할 수 있는 기능  
    \-> 만료될 떄 즉시 제거되는것을 보장안함.
-   대기 메시지 수 제한  
    \-> 3.1.0부터 메시지 최대 크기를 설정 할 수 있음.  
    \-> 최대 크기에 도달 하면 새로운 메시지가 추가 될 때 가장 먼저 받은 메시지를 삭제한다.
-   오래된 메시지 큐에서 제거  
    \-> 메시지를 오랫동안 소비 않을떄 자동으로 삭제하는 기능

queue 볼때 그냥 FIFO 아니야 하고 쉽게 봤는데 RabbitMq에서 쓰이는 queue는 진짜 한도 끝도 없다.

일단 개념 부터 Message Orderingm, Mirrored and Distributed Queues, Priorities, 

CPU Utilisation and Parallelism Considerations  이런것도 있고 여기서 파생되는 문서가 너무 많다... 

Priorities만 하더라도 AMQP 0-9-1 사양과 달리 RabbitMQ 대기열은 기본적으로 우선 순위를 지원하지 않는다. 

얼랭 프로세스에 의해 1~10까지만 사용한다고 추천된다. 

If priority queues are desired, we recommend using between 1 and 10. Currently using more priorities will consume more resources (Erlang processes). 

이런게 한두가지가 아니라 필요하면 그때 찾아보고 개념만 이해하는게 좋을거 같다.

#### \--- Binding ---

\- amqp 모델은 바인딩을 사용해서 큐와 익스체인지 관계를 정의한다. 

\- exchange와 queue간의 가상연결로 메시지가 익스체인지에서 큐로 이동할 수 있도록 하는 역할  

\- Binding과Binding key는 익스체인지가 **어떤 큐에** 메시지를 전달해야 하는지를 의미한다.

\- 익스체인지에 메시지를 발행할 때 애플리케이션은 라우팅 키 속성을 사용한다. 

요약 ::::: 

         1. 애플리케이션이 메시지를 익스체인지에게 발행할때 routing-key 속성을 사용한다. 

         2. exchange가 메시지를 큐로 전달하기 위해 메시지의routing-key를 binding 키에 맞춰서 평가한다.

         3. binding 키는 큐를 익스체인지에 연결하고 , 라우팅키를 평가하는 기준이다.  

출처 : 
[https://www.rabbitmq.com/tutorials/amqp-concepts.html](https://www.rabbitmq.com/tutorials/amqp-concepts.html)

[https://www.rabbitmq.com/amqp-0-9-1-reference.html](https://www.rabbitmq.com/amqp-0-9-1-reference.html)

[https://www.rabbitmq.com/queues.html](https://www.rabbitmq.com/queues.html)
