---
title: 'How Tomcat load spring web framework #1'
url: 1283.html
id: 1283
categories:
  - 미분류
date: 2017-06-23 14:48:01
tags:
---

How Tomcat load spring web framework
====================================

environment
-----------

*    Base project
*    Eclipse -> File -> New -> Other -> Spring Getting Started Content -> spring-mvc-showcase
*    Spring framework version : 4.2.2.RELEASE
*    Web Application Server : Apache Tomcat 8.0.36

Spring web framework deployment
-------------------------------

Web application에서 artifact를 servlet container에 올리는 과정을 deployment라고 표현한다. \[code lang=text\] 11-Apr-2017 02:49:11.284 정보 \[localhost-startStop-1\] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory /Users/thdghgns/Documents/workspace/web-server/apache-tomcat-8.0.43/webapps/manager 11-Apr-2017 02:49:11.310 정보 \[localhost-startStop-1\] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory /Users/thdghgns/Documents/workspace/web-server/apache-tomcat-8.0.43/webapps/manager has finished in 25 ms \[/code\]

### Deployment Descriptor (web.xml)

container/engine으로 배포되는 artifact에 대한 설정파일 Java Platform에서는 component, module, 또는 application이 어떻게 배포되어야 하는지에 대해 설명한다. deployment tool에게 module 또는 application이 특정한 container option, security setting으로 deploy할 것을 알려주고, 특정 설정 요구사항에 대해 설명해준다. XML은 deployment descriptor 파일 작성을 위해 사용된 문법이다. Web application에서 deployment descriptor는 **web.xml**이라고 불리며 web application root의 WEB-INF 디렉토리 하위에 위치해야 한다(**$root/WEB-INF/web.xml**) . Java EE application에서 deployment descriptor는 이름이 application.xml이어야 하며 application .ear file의 최상위 레벨의 META-INF 디렉토리 하위에 존재해야 한다. 참고자료 : 1. [Wikipia](https://en.wikipedia.org/wiki/Deployment_descriptor "Wikipedia DD") 2\. [Oracle Weblogic](https://docs.oracle.com/cd/E14571_01/web.1111/e13712/web_xml.htm#WBAPP502 "Oracle weblogic") 3\. [Google Appengine](https://cloud.google.com/appengine/docs/standard/java/config/webxml "Google appengine") 4\. [Opendocs](http://myblog.opendocs.co.kr/archives/436 "Opendocs")

### How Tomcat Load Spring (sketch)

![How Tomcat Load Spring (detail)](https://s3-ap-northeast-1.amazonaws.com/thdghgns/HowTomcatLoadSpring.svg) 링크 : [How Tomcat Load Spring (sketch)](https://s3-ap-northeast-1.amazonaws.com/thdghgns/HowTomcatLoadSpring.svg)

### ContextLoaderListener

#### 역할

1.  Spring의 root WebApplicationContext의 시작/종료를 위한 Bootstrap listener.
2.  `ContextCleanupListener`와 같이 간단하게 ContextLoader에게 위임한다.

`ServletContextListener`를 구현함으로써 servlet context에 대해 발생하는 이벤트를 받을 수 있게된다.

*   생성(초기화) : `public void contextInitialized(ServletContextEvent sce)`;
*   삭제 : `public void contextDestroyed(ServletContextEvent sce)`

#### javadoc

##### org.springframework.web.context.ContextLoaderListener

\[code lang=java\] /** * Bootstrap listener to start up and shut down Spring's root {@link WebApplicationContext}. * Simply delegates to {@link ContextLoader} as well as to {@link ContextCleanupListener}. * * This listener should be registered after {@link org.springframework.web.util.Log4jConfigListener} * in {@code web.xml}, if the latter is used. * * As of Spring 3.1, {@code ContextLoaderListener} supports injecting the root web * application context via the {@link #ContextLoaderListener(WebApplicationContext)} * constructor, allowing for programmatic configuration in Servlet 3.0+ environments. * See {@link org.springframework.web.WebApplicationInitializer} for usage examples. * * @author Juergen Hoeller * @author Chris Beams * @since 17.02.2003 * @see #setContextInitializers * @see org.springframework.web.WebApplicationInitializer * @see org.springframework.web.util.Log4jConfigListener */ public class ContextLoaderListener extends ContextLoader implements ServletContextListener { \[/code\]

##### javax.servlet.ServletContextListener

\[code lang=java\] /** * Implementations of this interface receive notifications about changes to the * servlet context of the web application they are part of. To receive * notification events, the implementation class must be configured in the * deployment descriptor for the web application. * * @see ServletContextEvent * @since v 2.3 */ public interface ServletContextListener extends EventListener { \[/code\] 참고 :

*   \[ContextLoaderListener\](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/ContextLoaderListener.html)
*   \[ServletContextListener\](https://tomcat.apache.org/tomcat-8.5-doc/servletapi/javax/servlet/ServletContextListener.html)

#### web.xml 설정

\[code lang=xml\] org.springframework.web.context.ContextLoaderListener \[/code\]

### ContextLoader

#### 역할

1.  root application context의 실제 초기화 작업을 수행
2.  web.xml에서 을 찾아서 context class type을 구분하여 \`contextClass\`로 설정.
3.  web.xml에서 `contextConfigLocation`을 찾아서 context instance의 값으로 전달
    1.  허용되는 value
        1.  commas 또는 spaces로 구분되는 여러개의 파일 경로
        2.  Ant-style의 표현식(eg."`WEB-INF/*Context.xml,WEB-INF/spring*.xml`")
        3.  defualt : "`/WEB-INF/applicationContext.xml`"
    2.  다중 값이 적용되기 때문에 순서에 따라 override가 됨.

참고 : [Spring framework javadoc - ContextLoader](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/ContextLoader.html)

### How Tomcat Load Spring (detail)

![How Tomcat Load Spring (detail)](https://s3-ap-northeast-1.amazonaws.com/thdghgns/SpringRootWebApplicationContextLoading.svg) 링크 : [How Tomcat Load Spring (detail)](https://s3-ap-northeast-1.amazonaws.com/thdghgns/SpringRootWebApplicationContextLoading.svg)

1.  Web Application Server(tomcat)이 DD(web.xml)을 읽어서 Listener(`ClassLoaderListener`)를 등록
2.  Web Application Server가 Servlet Context를 생성하면 servlet context 초기화 이벤트(ServletContextEvent)가 발생하고, 이벤트를 `ClassLoaderListener`가 이벤트를 받아서 `contextInitialized()`를 실행한다.
    1.  이때 받은 `ServletContextEvent`에서 `ServletContext`를 꺼내고 보면 실제 context는 `org.apache.catalina.core.ApplicationContext`이다.
3.  현재 `context==null`이기 떄문에 context를 생성한다.
    1.  별다른 설정이 없을 경우 `XmlWebApplicationContext`가 기본.
4.  context가 active가 아니고 root이기 때문에 parent==null이고 parent를 주려고 찾아도 없다.
5.  ServletContext를 WebApplicationContext의 servlet context로 설정한다.
6.  에 설정된 contextConfigLocation 설정 파일 위치를 읽어서 `StandardServletEnvironment`을 생성한다. `StandardServletEnvironment`는 내부적으로 `StandardEnvironment`를 확장하고, `StandardEnvironment`는 내부적으로 `AbstractEnvironment`를 확장한다.
    1.  property source를 customize.
        1.  servletConfigInitParams
        2.  servletContextInitParams
        3.  jndiProperties
        4.  systemProperties
        5.  systemEnvironment
    2.  property source를 init(새로 만들고 replace)
        1.  servletContextInitParams
    3.  refresh()
        1.  refresh 준비
            1.  active로 변경
            2.  property source를 다시 init
        2.  ConfigurableListableBeanFactory 획득. 없으면 생성
            1.  XmlBeanDefinitionReader
                1.  environment 설정
                2.  resource loader설정
                3.  entity resolver 설정
                4.  init
            2.  reader로 xml resource에서 bean definition을 읽음.
            3.  bean을 읽고
            4.  resource 닫고
        3.  postProcessBeanFactory
        4.  invokeBeanFactoryPostProcessors
        5.  registerBeanPostProcessors
        6.  initMessageSource
        7.  initApplicationEvntMulticaster
        8.  onRefresh -> theme 설정
        9.  registerListeners
        10.  finishBeanFactoryInitialization
            1.  bean 생성
    4.  Filter 등록
        1.  등록할 때 `StandardServletEnvironment`를 다시 customize하면서 property source 다시 설정
    5.  DispatcherServlet 등록
        1.  servletContext 생성
        2.  등록할 때 `StandardServletEnvironment`를 다시 customize하면서 property source 다시 설정

ApplicationContext의 특성
----------------------

### Application Context vs Bean Factory

#### 역할 및 기능

![](https://i.stack.imgur.com/TqcgB.jpg)

*   Bean Factory :
    *   Bean instantiation/wiring
*   Application Context :
    *   Bean instantiation/wiring
    *   Automatic BeanPostProcessor registration
    *   Automatic BeanFactoryPostProcessor registration
    *   Convenient MessageSource access (for i18n)
    *   ApplicationEvent publication

### Application Context Structure

![](https://slipp.net/wiki/download/attachments/25527577/ApplicationContext_2.jpg?version=1&modificationDate=1453340947000&api=v2)### Application Context Inheritance* Root Web Application Context : `ContextLoader`가 생성한 Application Context* DispatcherServlet Web Application Context : `Root Web Application Context`를 부모로 하는 child application context> parent에서는 child를 바라보지 못하고, child는 parent를 바라볼 수 있다.> 그래서 parent는 child context에 정의된 bean을 사용할 수 없고, child는 parent에 정의된 bean을 사용한다. 이때 한 parent에 속하는 child는 parent의 bean을 공유한다.![Application Context Inheritance](http://www.deroneriksson.com/deroneriksson-resources/tutorials/java/spring/introduction-to-the-spring-framework/configuring-root-web-application-context-in-spring-web-application/application-context-inheritance.png)> BeanPostProcessors, BeaFactoryPostProcessors는 child에게 상속되지 않는다. 이유는 scope가 `per-container`이기 때문이다.[참고자료 : 7.8.1 Customizing beans using a BeanPostProcessor](https://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-extension-bpp)### Root Web Application Context vs DispatcherServlet Web Application Context일반적으로 Root Applicaion Context에는 Services, DAOs와 같은 bean이 정의되고, DispatcherServlet Web Applicaion Context에는 servlet을 사용하는 Controller bean이 등록된다.![root context vs servlet context](http://www.deroneriksson.com/deroneriksson-resources/tutorials/java/spring/introduction-to-the-spring-framework/configuring-root-web-application-context-in-spring-web-application/root-web-application-context-vs-dispatcherservlet-web-application-context.png)

### Application Context Inheritance

*   Root Web Application Context : `ContextLoader`가 생성한 Application Context
*   DispatcherServlet Web Application Context : Root Web Application Context를 부모로 하는 child application context

parent에서는 child를 바라보지 못하고, child는 parent를 바라볼 수 있다. 그래서 parent는 child context에 정의된 bean을 사용할 수 없고, child는 parent에 정의된 bean을 사용한다. 이때 한 parent에 속하는 child는 parent의 bean을 공유한다. ![](http://www.deroneriksson.com/deroneriksson-resources/tutorials/java/spring/introduction-to-the-spring-framework/configuring-root-web-application-context-in-spring-web-application/application-context-inheritance.png) BeanPostProcessors, BeaFactoryPostProcessors는 child에게 상속되지 않는다. 이유는 scope가 'per-container'이기 때문이다. [참고자료 : 7.8.1 Customizing beans using a BeanPostProcessor](https://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-extension-bpp)

### Root Web Application Context vs DispatcherServlet Web Application Context

일반적으로 Root Applicaion Context에는 Services, DAOs와 같은 bean이 정의되고, DispatcherServlet Web Applicaion Context에는 servlet을 사용하는 Controller bean이 등록된다. ![](http://www.deroneriksson.com/deroneriksson-resources/tutorials/java/spring/introduction-to-the-spring-framework/configuring-root-web-application-context-in-spring-web-application/root-web-application-context-vs-dispatcherservlet-web-application-context.png) 참고자료 : [deroneriksson.com](http://www.deroneriksson.com/tutorial-categories/java/spring/introduction-to-the-spring-framework)