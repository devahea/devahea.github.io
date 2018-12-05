---
title: 2. Spring Batch Admin
url: 318.html
id: 318
categories:
  - spring batch
date: 2017-02-17 15:13:41
tags:
---

스프링 배치 어드민은 스프링은 오픈소스 프로젝트이며, 스프링배치에 대한 유저인터페이스를 웹 베이스로 제공합니다.

spring boot 와 spring admin을 연동시켜보았습니다.

\- maven dependency 추가

![image](https://ahea.files.wordpress.com/2017/02/image.png)

![image1](https://ahea.files.wordpress.com/2017/02/image1.png)

![image2](https://ahea.files.wordpress.com/2017/02/image2.png)

spring admin batch 에서 web 설정을 그대로 쓰기 위해 servlet-config, webapp-config 설정을 가져옵니다.

![image6](https://ahea.files.wordpress.com/2017/02/image6.png)

![image3](https://ahea.files.wordpress.com/2017/02/image3.png)

\- mainConfiguration을 import하고 bootRun을 하면 콘솔에서 접근가능한

url들이 나타납니다.

![image4](https://ahea.files.wordpress.com/2017/02/image4.png)

http:도메인/home 으로 들어가면 다음과 같이 페이지가 뜹니다.

![image5](https://ahea.files.wordpress.com/2017/02/image5.png)

spring IO 에서 spring batch에 대한 소식을 봤는데 버전 2.0.0.M1 이 릴리즈 되면서 다양한것들이 가능해졌습니다.

\[... will now support JSR-352 configured jobs. By dropping your XML based configuration in the `/META-INF/batch-jobs` directory as the spec requires, Spring Batch Admin will load the job to be launchable by the REST endpoints and the current UI. All of the monitoring aspects provided by Spring Batch Admin (viewing the executions, start/stop/restart, etc) are available.\]

2.0.0.M1 release로 바뀌면서 JSR-352(배치 처리를 위한 JSR 표준)를 더이상 지원하지 않습니다.

더이상 xml 베이스로 된 설정파일을 읽지 않고 UI에서 REST로 처리되고 모든 배치 진행중인 상황을 볼수 있습니다. 라고 써있네요.

batch의 진행상황을 보고 싶으면 batch admin을 추가해서 쉽게 확인 가능할것 같습니다.

spring batch 에 대한 기본 이해 후 batch admin의 사용법을 알아보겟습니다.

참고 : http://docs.spring.io/spring-batch-admin/getting-started.html