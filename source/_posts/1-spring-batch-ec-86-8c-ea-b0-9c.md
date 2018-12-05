---
title: 1. Spring batch 소개.
url: 209.html
id: 209
categories:
  - spring batch
date: 2017-02-12 16:17:58
tags:
---

0\. Why Spring Batch

기존 배치 프로그램 \[A씨의 배치 개발 일상\] A씨는 배치 개발을 위해 개발을 시작한다. A씨는 DB에 자신있어서 프로시저를 애플리케이션에서 호출해서 쓰고, 스케줄러는 DB 스케줄러를 쓰기로 한다. 기껏 JPA로 DB에 독립적으로 만들어놨는데 프로시저를 호출함으로 써 DB에 다시 종속적으로 포함됬다. 프로시저를 작성하고 DB 스케줄러를 등록하면서 개발을 완료했다. 테스트 과정에서 갑자기 이유없이 배치가 정지되는 상황이 생기고, 비정상적으로 중단된것을 알지 못했다. 확인 결과 한꺼번에 많은 양이 몰려서 발생한 에러였고, A씨는 밤새 비정상 정지되었던 배치를 다시 돌리는 로직을 추가 했으며 A씨는 배치가 돌았는지 쿼리를 실행해보고 확인한다. 현업에서 배치프로그램을 쓰다가 수정요청사항이 와서 A씨의 후임 개발자 B씨가 배치 프로그램을 수정해야 하는 상황이 발생했다. B씨는 소스를 보고 파악하려고 했으나 코드에서 프로시저를 호출하는 부분만 있고, 스케줄은 어떻게 도는지는 소스만 보고 파악을 하지 못한다. 또한 프로시저에 익숙하지 않은 B씨는 유지보수에 애로사항을 겪는다. B씨는 정상적으로 쿼리가 돌았는지 확인하기 위해 쿼리를 받았지만 실서버에서 쿼리를 날려보지 못하고, 로그를 보며 잘 실행되었는지 일일히 확인한다. 실제 본인이 겪은 경험과 주변에서 배치를 사용하면서 겪었던일들을 페르소나를 통해 풀어봤습니다. 이문제들을 Spring Batch와 Spring Admin을 통해서 해결해보도록 하겟습니다. 1. spring batch를 쓰고 프로시저를 제거 -> db 독립적(JPA를 사용한다는 가정) -> 프로시저를 알지 못해도 유지 보수 가능. 2. spring admin 사용으로 비정상 종료 확인 ->  Web based administration interface 3. batch와 스케줄러(Quartz, 티볼리, Control-M )사용으로 소스만 보고 로직 파악 4. 배치를 다시 돌리는 로직을 추가 -> Start/Stop/Restart 5. 한꺼번에 많은 양이 몰려서 발생한 에러 -> Chunk based processing Features 참고 : http://projects.spring.io/spring-batch/ Batch란

![batch.png](https://ahea.files.wordpress.com/2017/02/batch1.png)

Batch 는 일괄적으로 처리되는 용어이며 Spring batch는  생산성 향상, POJO 기반 개발 접근법, 그리고 사람들이 Spring Framework에서 알게 된 일반적인 사용 편의성을 기반으로 합니다. Quartz, 티볼리, Control-M 등과 같은 다양한 엔터프라이즈 스케줄러가 있고,  스프링 배치는 스케줄러를 교체하지 않고 스케줄러와 함께 작동하도록 설계되었습니다. Spring Batch는 로깅 / 추적, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 건너 뛰기 및 자원 관리를 포함하여 많은 양의 레코드를 처리하는 데 필수적인 재사용 가능한 기능을 제공합니다.

1\. Spring Batch Introduction

엔터프라이즈 도메인 내의 많은 애플리케이션은 비즈니스 운영을 수행하려면 대량의 처리가 필요합니다.  이러한 비즈니스 운영은 사용자 상호 작용 없이 방대한 양의 정보를 자동으로 처리합니다.

Batch 처리는 매일 수십억건의 트랜잭션을 처리하는 데 사용됩니다. 스프링 배치는 매일 기업 시스템의 일상적인 작업에 필수적인 견고한 배치 애플리케이션을 개발할 수 있도록 설계된 경량의 포괄적인 배치 프레임워크입니다.

스케줄러를 교체하지 않고 스케줄러와 함께 작동하도록 설계되어있고, Spring Batch는 로깅, 추적, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 건너 뛰기 및 자원 관리를 포함하여 많은 양의 레코드를 처리하는 데 필수적인 재사용 가능한 기능을 제공합니다.

복잡하고 대량의 대량 배치 작업은 매우 확장 가능한 방식으로 프레임워크를 활용하여 상당한 양의 정보를 처리할 수 있습니다.

Spring batch 는 이미 컨설팅 회사인 엑센츄어(Accenture)의 참여로 2008 년도쯤에 35개의 회사에서 안정적으로 쓰고 있다고 발표가 나서 안정성은 이미 보장되는 상황입니다.

~~~. Spring Batch already is in use at more than 35 Accenture clients

https://www.cnet.com/news/accenture-springsource-team-up-but-heres-the-real-news/

1.3 Spring Batch Architecture

Spring Batch는 확장 성과 다양한 최종 사용자 그룹을 염두에두고 설계되었습니다. 아래의 그림은 확장성을 지원하고 개발자가 쉽개 사용가능한 다층 아키텍처의 개요를 보여 줍니다.

![batch](https://ahea.files.wordpress.com/2017/02/batch.png)

이 아키텍처는 애플리케이션, 코어, 인프라 스트럭처 등 세가지 주요 구성 요소를 중점적으로 다룹니다.

애플리케이션에는 Spring Batch를 사용하는 개발자가 작성한 custom 코드나 모든 batch 작업이 포함되어 있습니다.

Batch Core는 배치 작업을 시작하고 제어하는데 필요한 핵심 런타임 클래스를 포함합니다.

여기에는 JobLauncher, Job, Step이 포함됩니다. 두 애플리케이션과 코어는 common infrastructure 위에 구축됩니다. 이러한 infrastructure 에는 common readers and writers 가 포함되어 있으며, 애플리케이션 개발자와 코어 프레임워크 모두에서 사용되는 RetryTemplate와 같은 서비스를 제공합니다.

참고 : http://docs.spring.io/spring-batch/reference/html/