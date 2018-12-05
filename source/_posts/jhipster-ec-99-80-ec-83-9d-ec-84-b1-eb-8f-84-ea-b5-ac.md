---
title: JHipster와 생성 도구
url: 1750.html
id: 1750
categories:
  - 미분류
date: 2018-08-18 08:23:00
tags:
---

이전 시간에는 JHipster의 프로젝트 생성에 대해서 다루었습니다. 이번 시간에는 적극적으로 `Sub Generator`를 활용 하여 Entity, Srping Controller, Spring Service를 생성하는 방법과 JDL, UML를 이용 하여 Entity들을 파일로 Impot, Export하는 방법에 대해 알아 봅시다. 또한 MSA사용시 Cloud용 Ganerator를 알아 봅시다.

Entity 생성
=========

데이터를 담을 수 있는 객체를 생성 할 수 있으며, DB와의 연관관계 및 Sub Genorator를 통해 Entity의 `Field`와 데이터의 길이 및 제한을 위해 `Field의 Validation Check`, `다른 Entity와의 연관관계 지정` 까지 가능 합니다. \[code lang=bash\] $ jhipster entity Entity이름 --옵션 \[/code\]

*   Field를 생성 할 것인가?
    *   name, type, validate 설정
*   연관관계를 맺을 것인가?
    *   연관관계설정
*   비즈니스 로직용 클래스를 만들 것인가?
    *   Controller만, Controller와 Service Class, Controller와 Service Interface
*   Entity의 페이징 기능 사용 할 것인가?
    *   무한스크롤, 페이지넘버링크

크게 4가지 질문에 답을 하면, 선택에 따라

*   Java 파일 : Entity, DTO, Service, Controller의 java
*   설정 파일 : liquibase의 xml
*   Web 파일 : Entity의 ts

파일 들이 생성 됩니다.

Spring Controller 생성
====================

URL 매핑 관련 Spring Controller 생성 입니다. \[code lang=bash\] $ jhipster spring-controller Controller명 \[/code\]

*   컨트롤러를 만들 것인가?
*   추가할 액션은(URL명)?
    *   HTTP method는?

크게 2가지 질문만 답을 하면, 선택에 따라 Srping Controller 파일이 생성 됩니다.

Spring Service 생성
=================

비즈니스 로직을 담을 Spring Service 생성 입니다. \[code lang=bash\] $ jhipster spring-service Service명 \[/code\]

*   인터페이스 연계 Service를 만들 것인가?

한가지 질문만 있으며, 선택에 따라 Service용 인터페이스와 서비스 클래스파일이 생성 됩니다.

> Entity생성은 생산성에 좋지만, Controllrt와 Service는 직접 생성하는게 더 좋은거 같습니다.

JDL 사용
======

JDL(JHipster Domain Language)은 Entity의 생성을 많이 할 경우 유용하며, JDL파일을 가지고 도메인 다이어그램을 그릴수 있는 [JDL Studio](https://start.jhipster.tech/jdl-studio/), [JDL IDE Plugin](https://www.jhipster.tech/jhipster-ide/)라는 툴이 있어 협업 시 효과적 입니다. \[code lang=bash\] //프로젝트에 JDL 삽입 시 $ jhipster import-jdl jdl파일명 //프로젝트에서 JDL 추출 시 $ jhipster export-jdl jdl파일명 \[/code\] 이 기능을 활용 하여 자유자재로 Import와 Export하여 팀원들과 공유 및 커뮤니케이션을 할 수 있습니다. [JDL Studio](https://start.jhipster.tech/jdl-studio/)는 웹서버로 제공 하며, [JDL IDE Plugin](https://www.jhipster.tech/jhipster-ide/)은 현재 이클립스와 비주얼 스튜디오 코드만 존재 합니다.

UML 사용
======

UML을 활용한 Import와 Export가 가능하며, 이 기능은 사용 할 시 Genorator 설치 가 필요 합니다. \[code lang=bash\] $ yarn add global jhipster-uml \[/code\] 지원 가능한 UML 서비스는 아래와 같습니다.

*   [Modelio](https://www.modeliosoft.com/en/)
*   [UML Designer](http://www.umldesigner.org/)
*   [GenMyModel(유료)](https://www.genmymodel.com/)

\[code lang=bash\] $ jhipster-uml UML파일 \[/code\]

Entity사용에 관한 Ganorator정리
========================

살펴본 결과 `한 개의 Entity를 생성 시엔 Entity Generator를 사용`하는 게 좋고, `다량의 Entity 생성은 JDL을 통해 생성`하는 게 좋습니다. 그리고 협업하시는 분과의 `커뮤니케이션 시 JDL Studio, JDL IDE Plugin의 도메인 다이어그램을 활용`하시면 좋을 거 같습니다.

Cloud 생성
========

Cloud 사용을 위한 다양한 Ganerator를 제공 하며 종류는 아래와 같습니다.

*   Cloud Foundry
*   Heroku
*   Kubernetes
*   OpenShift
*   Rancher
*   AWS
*   Boxfuse
*   docker-compose

Cloud 활용은 https://www.jhipster.tech/production/ 이페이지를 참조 하시면 됩니다. docker-compose는 뒤에서 MSA설명 할 때 다시 설명 드리겠습니다.

그 외 생성 도구
=========

위에서 소개해드린 생성 도구 이 외에도 JHipster 공식 Sub Generator는 https://github.com/jhipster/generator-jhipster/tree/master/generators 이 페이지에서 확인 하실 수 있습니다.