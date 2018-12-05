---
title: Java JDBC
tags:
  - java
  - JDBC
url: 376.html
id: 376
date: 2017-02-20 09:52:02
---

BC자바에서 데이터베이스에 접속 할수 있는 API를 제공하는데 그게  [**JDBC**(Java Database Connectivity)](https://ko.wikipedia.org/wiki/JDBC) 입니다.

![untitled-diagram](https://ahea.files.wordpress.com/2017/02/untitled-diagram.png)

자바 JDBC와 DB벤더들

JDBC API는 Java에서 가장 로우레벨의 데이터베이스 API 입니다. 그래서 SQL을 실행하는 데도 매우 번잡한 코드가 필요하고 DB에 따라 일관성없는 예외체크를 해야 하며 SQL은 코드 내에서 문자로 제공해야 하는 불편을 감수해야 한다. 커넥션과 같은 공유 리소스를 제대로 처리하지 않으면 시스템의 자원이 바닥나는 심각한 버그를 심어놓을 수도 있다.

![springjdbc](https://ahea.files.wordpress.com/2017/02/springjdbc.png)

Spring JDBC

spring 에서는 jdbc를 확장해서 만든 jdbctemplate을 이용하여 데이터베이스에 접근을 합니다.

jdbctemplate은 JDBC API를 가지고 템플릿콜백패턴을 통해 간결함과 리소스 처리를 함으로써 비즈니스로직에 집중할수 있게 하였고 데이터베이스마다 다른 exception핸들링을 통하여 데이터베이스 예외처리를 획일화 하였습니다.

![DataAccessException.gif](https://ahea.files.wordpress.com/2017/02/dataaccessexception.gif)

스프링 Exception 핸들링

spring data 는 JDBC뿐만 아니라 JPA와 각각의 클라이언트를 제공하던 NoSQL들도 제공 함으로써 Spring에서 가장 대표적으로 사용하는 Dao Access 프레임워크가 되었습니다.

Repositories, Templates, Object Mapping과 트랜잭션동시지원, 간편한 설정등의 기능을 더하고 확장 하였습니다.

![Image.png](https://ahea.files.wordpress.com/2017/02/image8.png)

Spring Data와 서브 프로젝트들

spring data 서브프로젝트 중에서는 Repository만 구현해도 자동으로 CRUD및 Paging, sort 기능을 자동으로 생성되는 프로젝트들도 있습니다.

spring data의 기능은 Repository에 crud, paging, sort 기능을 함유 커스텀 Repository 하고 있고 각종 유틸과 QueryDSL에 관련된 서포트툴이 있어

QueryDSL의 TypeSafe한 쿼리문을 작성하기에 용이 합니다.

![querydsl](https://ahea.files.wordpress.com/2017/02/querydsl.png)

QueryDSL 동작

[QueryDSL](http://www.querydsl.com/static/querydsl/4.0.1/reference/ko-KR/html_single/)은 정적 타입을 이용해서 SQL과 같은 쿼리를 생성할 수 있는 프레임워크다. 문자열이나 XML에 쿼리를 작성하는 대신 QueryDSL이 제공하는 플루언트(Fluent) API를 이용해서 쿼리를 생성할 수 있다.

*   IDE의 코드 자동 완성 기능 사용
*   컴파일 시점에서 잘못된 쿼리를 허용하지 않음
*   도메인 타입과 프로퍼티를 안전하게 참조할 수 있음
*   도메인 타입의 리팩토링을 더 잘 할 수 있음

예제 프로그램을 통해서 자바 JDBC, Spring JDBCTemplate, Spring data jdbc 알아 보겠습니다.