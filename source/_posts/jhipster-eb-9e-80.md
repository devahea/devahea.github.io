---
title: JHipster란?
tags:
  - devops
  - docker
  - java
  - JHipster
  - spring
url: 1719.html
id: 1719
date: 2018-07-29 14:24:48
---

JHipster란?
==========

![](https://i2.wp.com/keyholesoftware.com/wp-content/uploads/jHipster.jpg) 간단히 정의 하면 `자바 기반 생성(generate) 개발 플랫폼` 이라고 말씀 드릴 수 있습니다. 기존 뛰어난 생산성으로 흥한 개발 플랫폼인 Ruby의 `Ruby on Ralse`나 Node.js의 `Meteor.js`등 애플리케이션 개발을 생성 도구 활용으로 인기를 끌었습니다. 그동안 Spring 진영에서도 생성 도구를 활용한 개발이 없어 생산성에서 뒤쳐졌지만, JHipster에서 만들게 되어 Spring에서도 빠른 생산성을 확보하게 되었습니다. 생성 도구는 Yeoman이라는 기존 웹 보일러플레이트 프로젝트 생성 도구인데 JHipster에서 Yeoman기반으로 만들어 활용 하였습니다.

Yeoman이란?
=========

![](https://cdn-images.threadless.com/threadless-media/artist_shops/shops/yeoman/products/633217/shirt-1529592223-b157e98a44b27cc571605d6297f9f9e9.png) 웹 개발을 프레임워크 및 라이브러리들을 통합하여 쉽게 프로젝트를 생성 할 수 있는 Tool 입니다. \[code lang=bash\] $ npm i -g yo $ npm i -g generator-원하는도구 \[/code\] 기본적으로 Node.js가 설치 되어 있어야 하며, `yo`를 설치 후 원하는 도구를 설치 하면 됩니다. Yeoman에서 제공하는 API를 활용하면 쉽게 CLI기반 Generate가 가능 하므로 관심 있으신 분들은 http://yeoman.io/authoring/index.html 이 URL을 참조 하셔서 개발 하시면 됩니다.

JHipster Sample Project
=======================

준비
--

JHipster를 사용하기 전 필수로 설치할 프로그램을 소개 합니다.

*   Java
*   Git
*   Node.js
*   Yarn
*   Yeoman

Java와 Git, Node.js는 OS에 맞추어 설치 하시고 Yarn과 Yeoman은 Node.js와 함께 설치된 npm으로 설치 하시면 됩니다. \[code lang=bash\] $ npm i -g yo $ npm i -g yarn $ yarn global add generator-jhipster \[/code\] https://www.jhipster.tech 에 접속 하여 Quick Start를 보고 샘플 프로젝트 생성을 진행 하겠습니다. JHipster 문서 보실때 헷갈릴까봐 한가지 짚고 넘어 가겠습니다. 명령어 사용 시 옛날 방식과 최근 방식에 차이점이 있는데 yo를 쓰고 안쓰고 차이 입니다. 입맛에 맞게 골라서 사용하시면 될거 같습니다.

*   프로젝트 생성 시

\[code lang=bash\] //옛날 방식 $ yo jhipster //최근 방식 $ jhipster \[/code\]

*   추가 기능 사용 시

\[code lang=bash\] //옛날 방식 $ yo jhipster:추가기능 //최근 방식 $ jhipster 추가기능 \[/code\]

생성
--

샘플프로젝트 생성 시 선택 가능한 내역 입니다. **굵게** 표시한건 이번 프로젝트 생성 시 선택한 것 입니다.

*   어떤 타입의 프로젝트를 생성하는가?
    *   **모놀로식 애플리케이션**
    *   마이크로서비스 애플리케이션
    *   마이크로서비스 게이트웨이
    *   JHipster UAA Server
*   어떤 인증을 사용 할 것인가?
    *   **JWT**
    *   Oauth2 / OIDC
    *   HTTP Sesstion Authentication
*   어떤 DB를 사용 할 것인가?
    *   **SQL(H2, MySQL, MariaDB, PostgreSQL, Oracle, MSSQL)**
    *   MongoDB
    *   Couchbase
    *   Cassandra
*   (SQL 선택시 나옴) dvelopment와 production에 사용할거 선택
*   Spring Cache를 사용할 것인가?
    *   Ehcach
    *   Hazelcast
    *   Memcached
    *   **사용안함**
*   어떤 빌드도구를 쓸 것인가?
    *   Maven
    *   **Gradle**
*   다른 기술을 사용할 것인가?(모두 선택 가능)
    *   엘라스틱서치 기반 검색
    *   웹소켓
    *   OpenAPI-generator
    *   Kafka
*   어떤 Front를 사용 할 것인가?
    *   Angular 6
    *   **React**
*   추가 테스트 프레임워크를 사용 할 것인가?(모두 선택 가능)
    *   Gatling
    *   Cucumber
    *   Protractor
*   어떤 기본 언어를 선택 할 것인가?
    *   **한국어**
    *   영어
    *   기타 등등
*   추가로 지원할 언어는?(모두 선택 가능)
    *   **영어**
    *   나머지 선택지는 위와 동일

본격적으로 생성을 해보겠습니다. \[code lang=powershell\] PS C:\\Users\\a\\jhipsterProjects> mkdir myApp; cd myApp PS C:\\Users\\a\\jhipsterProjects\\myApp> \[/code\] ![](https://user-images.githubusercontent.com/6037055/43363131-6739432a-9338-11e8-88ab-e67f65f64b9d.png) ![](https://user-images.githubusercontent.com/6037055/43363132-6772ba7e-9338-11e8-9d92-7373b8dfce3c.png) ![](https://user-images.githubusercontent.com/6037055/43363133-67ae6d12-9338-11e8-972a-3eaacf0252ab.png) ![](https://user-images.githubusercontent.com/6037055/43363134-68561d00-9338-11e8-898c-d3e064410f55.png) ![](https://user-images.githubusercontent.com/6037055/43363135-68837304-9338-11e8-8567-1c57306f30d0.png) ![](https://user-images.githubusercontent.com/6037055/43363136-68b06cba-9338-11e8-9b85-4e598c6b5100.png) ![](https://user-images.githubusercontent.com/6037055/43363137-68dacd16-9338-11e8-9219-6b28739e03df.png) ![](https://user-images.githubusercontent.com/6037055/43363138-6906a3d2-9338-11e8-964b-0f321ad0a7d0.png) ![](https://user-images.githubusercontent.com/6037055/43363130-670d28f8-9338-11e8-92f0-0ae781eaaec9.png) Spring Boot + React앱이 만들어 졌고 다음시간엔 JDL과 CLI을 통한 Entity+Controller+Service생성을 알아 보겠습니다.