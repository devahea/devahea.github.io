---
title: '[초급편] JAVA HIPSTER 공부하기'
url: 1743.html
id: 1743
categories:
  - 미분류
date: 2018-08-17 02:39:55
tags:
---

_**프로그래밍 초급자가 바라본 JAVA HIPSTER - "아~ 이건 언제 다 공부하냐"**_ **1\. 가장 먼저 한 일 - "https://www.jhipster.tech" 사이트 둘러보기**

*   (의문을 가지다) : "Jhipster 는 무엇이냐"
*   (공식적인 답변) : "Jhipster 는 스프링부트와 앵귤러(또는 리액트) 기반 웹 어플리케이션 또는 스프링 마이크로서비스를 생성하고 개발하고 배포하기 위한 개발 플랫폼이다" (JHipster is a development platform to generate, develop and deploy Spring Boot + Angular/React Web applications and Spring microservices)

**2\. 두번째로 한 일 - Jhipster Quick Start (16분 동영상) 따라해보기** \[jhipster를 설치하기 위한 6가지 방법\] - 한개씩 설치 방법을 포스팅할 예정

*   Jhipster Online : "https://start.jhipster.tech/#/"
*   Local installation with Yarn
*   Local installation with NPM
*   Installation with a package manager ( Homebrew on MacOSX, Chocolatey on Windows)
*   Vagrant box installation
*   Docker installation

: Jhipster 를 설치하는 방법은 총 6가지가 있으며, 동영상에서는 2번째 방법인 Local installation with Yarn 방법을 사용하였다.

*   java 설치 : Oracle website 에서 자바 8 버전 이상을 다운로드 및 설치
*   Node 설치 : LTS 64-bit version을 다운로드 및 설치
*   Yarn 설치 : 운영체제별 맞춤 설치방법 제공(윈도우의 경우 .msi 제공)
*   Yeoman 설치 : Jhipster Marketplace 이용 시 -> "yarn global add yo"
*   Jhipster 설치 : "yarn global add generator-jhipster"

* * *

*   New Jhipster application 설치
*   신규 폴더 생성 : (Ex) mkdir myapplication
*   신규 생성 폴더 이동 : (Ex) cd myapplication
*   신규 어플리케이션 생성을 위한 명령어 입력 : (Ex) jhipster

===== 어플리케이션 생성 시 질문 내역 =====

1.  Which type of application would you like to create?

*   Monolithic application: this a classical, one-size-fits-all application. It’s easier to use and develop, and is our recommended default.
*   Microservice application: in a microservices architecture, this is one of the services.
*   Microservice gateway: in a microservices architecture, this is an edge server that routes and secures requests.
*   JHipster UAA server: in a microservices architecture, this is an OAuth2 authentication server that secures microservices. Refer to the JHipster UAA documentation for more information.

1.  What is the base name of your application?
    
2.  What is your default java package name?
    
3.  Do you want to use the jhipster registry to configure, monitor and scale your application?
    
4.  Which type of authentication would you like to use?
    

*   JWT authentication: use a JSON Web Token (JWT), which is the default choice
*   HTTP Session Authentication: the classical session-based authentication mechanism, like we are used to do in Java (this is how most people use Spring Security).
*   OAuth 2.0 / OIDC Authentication: this uses an OpenID Connect server, like Keycloak or Okta, which handles authentication outside of the application.
*   Authentication with JHipster UAA server: this uses a JHipster UAA server that must be generated separately, and which is an OAuth2 server that handles authentication outside of the application.

1.  Which type of database would you like to use?

*   No database (only available when using a microservice application)
*   An SQL database (H2, MySQL, MariaDB, PostgreSQL, MSSQL, Oracle), which you will access with Spring Data JPA
*   MongoDB
*   Cassandra
*   Couchbase

1.  Which production database would you like to use?
    
2.  Which development database would you like to use?
    

*   H2, running in-memory. This is the easiest way to use JHipster, but your data will be lost when you restart your server.
*   H2, with its data stored on disk. This is a better option than running in-memory, as you won’t lose your data upon application restart.
*   The same database as the one you chose for production: it’s a bit more complex to set up, but it should be better in the end to work on the same database as the one you will use in production. This is also the best way to use liquibase-hibernate as described in the development guide.

1.  Do you want to use the spring cache abstraction?
    
2.  Do you want to use Hibernate 2nd level cache?
    
3.  Would you like to use Maven or Gradle?
    
4.  Which other technologies would you like to use?
    

*   API first development using swagger-codegen
*   Search engine using ElasticSearch
*   Clustered HTTP sessions using Hazelcast
*   WebSockets using Spring Websocket
*   Asynchronous messages using Apache Kafka

1.  Which Framework would you like to use for the client?

*   Angular
*   React

1.  Would you like to use the LibSass stylesheet preprocessor for your CSS?
    
2.  Would you like to enable internationalization support?
    
3.  Which testing frameworks would you like to use?
    

*   Performance tests using Gatling
*   Behaviour tests using Cucumber
*   Angular integration tests with Protractor

1.  Would you like to install other generators from the jhipster Marketplace?

* * *

*   신규 소스 파일 생성 후, Maven 또는 Gradle 명령어를 이용하여 어플리케이션 기동

*   Maven 기반 리눅스, 맥, 윈도우 파워쉘의 경우 :  ./mvnw
*   Maven 기반 윈도우즈 cmd의 경우 : mnvw
*   Gradle 기반 리눅스, 맥, 윈도우 파워셀의 경우 : ./gradlew
*   Gradle 기반 윈도우즈 cmd의 경우 : gradlew

**3\. 세번째로 한 일 - JHipster UML 과 JDL Studio 사용하여 Entity 생성하기**

*   Jhipster UML는 무엇(?) : "jhipster의 서브프로젝트"로서  UML Editor에서 Jhipster-UML 다이어그램 생성을 지원, 현재 공식적으로 지원하는 Editor는 다음과 같다.
    *   Modelio
    *   UML Designer
    *   GenMyModel
*   Jhipster UML  사용하기
    *   step 1 - xmi 확장자의 파일 형태로 class 다이어그램을 추출한다.
    *   step 2 - Jhipster 어플리케이션의 루트 폴더로 xmi 파일을 업로드한 후, 명령어를 실행한다. "jhipster-uml
*   JDL Studio는 무엇(?) : "jhipster에서 공식적으로 지원하는 온라인 툴로서 도메인 기반 언어인 JDL을 이용하여 객체 및 관계를 정의한다.
    *   step 1 - jdlstudio("https://start.jhipster.tech/jdl-studio/") 사이트에서 객체 및 관계를 정의한다.
    *   step 2 - Download text file of this JDL 메뉴를 이용하여 .jh 파일을 생성한다.
    *   step 3 - 해당 파일을 jhipster 어플리케이션의 루트 폴더로 업로드한 후, 명령어를 실행한다. "jhipster import-jdl

1.  미쳐 정리하지 못한 것들

*   추가적인 기능연계 : (Ex) 스프링시큐리티, 엘라스틱서치, 웹소켓, 캐시 등
*   배포 기능 : 클라우드에 서비스하기(heroku, aws, cloud Foundry 등)