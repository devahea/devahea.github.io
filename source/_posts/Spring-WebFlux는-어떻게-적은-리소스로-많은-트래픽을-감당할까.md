---
title: Spring WebFlux는 어떻게 적은 리소스로 많은 트래픽을 감당할까?
date: 2019-04-21 13:44:32
categories:
 - spring
tags:
- Spring WebFlux
- Event Driven
- Reactive Programming
---

![webflux-performance](https://3.bp.blogspot.com/-3i759KJap_U/We6baQQFc2I/AAAAAAAAfTs/0G7gLgD2BWsmVbPluFFoeGhViOafX1QqgCLcBGAs/s1600/Boot1VsBoot2.png)

위 그림은 [DZone](https://dzone.com/articles/raw-performance-numbers-spring-boot-2-webflux-vs-s) 게시글 중 하나인 Spring WebFlux를 이용한 Boot2와 Spring MVC를 이용한 Boot1을 비교한 그래프이다.
유저가 적을 때에는 성능은 별반차이가 없지만, 유저가 늘어나면 늘어날수록 극명한 성능차이를 보여주고 있다. 어떻게 이런 차이가 일어날 수 있을까?

이는 Event-Driven과 Non Blocking I/O를 활용하여 적은 리소스로 많은 트래픽을 처리할 수 있다. 


### 앞으로 다룰 내용
* Event-Driven
* Blocking, Non Blokcing I/O
* Spring WebFlux
* Reactive Programming