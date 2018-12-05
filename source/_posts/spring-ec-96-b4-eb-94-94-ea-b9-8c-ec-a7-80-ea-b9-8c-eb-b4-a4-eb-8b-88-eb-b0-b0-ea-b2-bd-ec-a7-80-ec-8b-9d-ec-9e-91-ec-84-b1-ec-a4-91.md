---
title: 'Spring, 어디까지 까봤니? (배경지식, Servlet)'
tags:
  - java
  - servlet
url: 1704.html
id: 1704
categories:
  - 미분류
  - 공개스터디
date: 2018-05-11 23:59:16
---

Java Servlet
------------

최근에 java에 입문한 쥬니어라면 "Java Servlet"에 대해 어디선가 들어봤지만, 누구도 자세히 알려주지 않습니다. 주로 회사에서 배우다보면 업무에 필요한 프레임워크 레벨부터 배우기 때문입니다. Java Servlet의 소스를 보면 아래와 같이 설명되어 있습니다. \[code language="java"\] package javax.servlet; import java.io.IOException; /** * Defines methods that all servlets must implement. * * * A servlet is a small Java program that runs within a Web server. Servlets * receive and respond to requests from Web clients, usually across HTTP, the * HyperText Transfer Protocol. * * * To implement this interface, you can write a generic servlet that extends * `javax.servlet.GenericServlet` or an HTTP servlet that extends * `javax.servlet.http.HttpServlet`. \* \* \* This interface defines methods to initialize a servlet, to service requests, * and to remove a servlet from the server. These are known as life-cycle * methods and are called in the following sequence: *

*1.  The servlet is constructed, then initialized with the `init` \* method. *
2.  Any calls from clients to the `service` method are handled. *
3.  The servlet is taken out of service, then destroyed with the * `destroy` method, then garbage collected and finalized. *

\* \* \* In addition to the life-cycle methods, this interface provides the * `getServletConfig` method, which the servlet can use to get any * startup information, and the `getServletInfo` method, which allows * the servlet to return basic information about itself, such as author, * version, and copyright. * * @version $Version$ * * @see GenericServlet * @see javax.servlet.http.HttpServlet */ public interface Servlet { \[/code\] 우선 2가지를 자세히 볼 필요가 있습니다.

1.  "package javax.servlet;" Servlet 인터페이스가 속한 패키지는 javax.servlet이다. 곧 Servlet은 java extension 기능이고, java 자체적으로 제공하는 기능인 것입니다.
2.  "A servlet is a small Java Program that runs within a Web server" Servlet은 웹서버에서 동작하는 작은 프로그램이다.

자세히 보면 Servlet의 동작 방식도 알 수 있습니다. 생성자로 인해 생성되면, "init" 메소드가 실행되어 초기화되고, client로부터의 요청은 "service" 메소드가 처리하고, 제거될 때는 "destroy" 메소드가 실행됩니다. 물론 깊게 보려면 각 메소드별로 소스를 봐야 하겠지만, javadoc만 정독해도 클래스의 역할과 흐름을 어느정도 파악할 수 있습니다.

Servlet Container (Web Container)
---------------------------------

"Servlet Container"라는 이름에서 유추해보면 무언가 Servlet을 여러개 포함하고 있을 것 같습니다. "Servlet Container"는 개념적인 이야기이기 때문에 위키피디아의 설명을 찾아보았습니다. ![web_container](https://ahea.files.wordpress.com/2018/05/web_container.png)   위키피디아 설명의 가장 상단에 작성된 요약본이지만, 중요한 얘기는 다 나왔습니다.

1.  서블릿과 상호 작용한다.
2.  서블릿의 생명주기를 관리해준다.
3.  URL과 특정 서블릿을 맵핑하여 URL 요청이 올바른 접근 권한을 갖도록 보장한다.

위에서 길게 설명한 내용(Java servlet, Servlet Container)을 간략하게 그림으로 살펴보면 아래와 같습니다. ![servlet](https://ahea.files.wordpress.com/2018/05/servlet.png) 즉, "Servlet Container는 Servlet의 생명주기를 관리하여 Servlet을 생성해서 가지고 있고, 생성한 Servlet에 특정 URL을 맵핑하여 Client의 요청을 넘겨준다. 그리고  Servlet은 Servlet Container가 전달해준 요청을 적절하게 처리하고 응답을 생성하여 Servlet Container를 통해 회신해준다." 정도로 정리할 수 있겠습니다.