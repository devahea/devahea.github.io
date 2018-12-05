---
title: '4. Spring batch, boot 연동 및 처리 과정.'
url: 420.html
id: 420
categories:
  - spring batch
date: 2017-02-20 19:28:01
tags:
---

spring batch 와 spring boot를 연동하고 프로세스 과정을 살펴보겟습니다.

sample 데이터 추가.

![image](https://ahea.files.wordpress.com/2017/02/image9.png)

schema-@@platform@@.sql으로 플랫폼에 맞춰서 sql문이 돌아간다.

-all이면 모든 플랫폼에 대해 기본값으로 설정됨.

ex>schema-mysql.sql

![image1](https://ahea.files.wordpress.com/2017/02/image12.png) ![image2](https://ahea.files.wordpress.com/2017/02/image22.png)

간단하게 firstName 과 lastname을 대문자로 만들어주는 PersionItemProcessor를 만들었습니다.

PersionItemProcessor는 ItemProcessor의 인터페이스를 구현받습니다.

Item이 나오는데 Item은 처리할 데이터의 가장 작은 구성 요소 입니다.

![image3](https://ahea.files.wordpress.com/2017/02/image32.png)

이제 실제 Batch job을 돌려봅시다. Spring batch는 개발자가 비지니스 로직에 집중할수 있도록 많은 유틸리티 클래스를 제공합니다.

`EnableBatchProcessing`은 jobBuilderFactory와 stepBuilderFactory 빈을 제공합니다.

Job은 step 으로 이루어져 있고 Step은 reader, processor, writer로 이루어져 있습니다.

JobInstance 는 Job + Job Parameter이기 때문에 Job Parameter를 자동으로 Increment 하기 위해서 사용합니다.

Step에서는 한번에 쓸 데이터 양을 정의 합니다. 이 경우에는 한번에 최대 10개 item의 record를 기록합니다."chunk"의 입력 및 출력 유형을 나타내며, ItemReader<~>및 ItemWriter<~> 와 일치합니다.

![image4](https://ahea.files.wordpress.com/2017/02/image42.png)

아래 그림을 보면 위에 프로세스와 비슷하게 흘러가는걸 확인할수 있습니다.

![image](https://ahea.files.wordpress.com/2017/02/image7.png)

reader   -   sample-data에 있는 csv파일을 읽어서 하는 부분이니 이런식으로 읽는구나만 참조 하면 될 것 같습니다.

writer - date를 db에 쓰는 부분 입니다.

![image5](https://ahea.files.wordpress.com/2017/02/image52.png)

  Processor 부분인데 실제 비지니스 로직이 포함 되는 부분입니다.

![Image6.png](https://ahea.files.wordpress.com/2017/02/image62.png)

job 에 걸린 listener를 보면 끝난 상태를 체크하는 부분을 JobExecution에서 status 를 가져오는것을 알수 있습니다.

![Image7.png](https://ahea.files.wordpress.com/2017/02/image71.png)

Job Execution에 대해서는 이전 글을 참고하시면 됩니다.

결과 화면 :

![Image10.png](https://ahea.files.wordpress.com/2017/02/image10.png)

배치 처리는

1.  main 메소드가 있는 jar 만들거나
2.  웹 애플리케이션에 포함시킨다.

jar 만드는 방법은 구글링 참고.

Spring batch 참고 : [https://spring.io/guides/gs/batch-processing](https://spring.io/guides/gs/batch-processing/)

위에 사이트에서 소스를 받아서 돌렸는데 h2 메모리 디비 말고 mysql을 쓰고 싶어서

datasource에

![Image8.png](https://ahea.files.wordpress.com/2017/02/image81.png)

다음과 같이 세팅했습니다.

BasicDataSource 를 import하기 위해서 maven에 dependency를 추가하면 끝.

![Image9.png](https://ahea.files.wordpress.com/2017/02/image91.png)

\-\- 기타 \-\- 1\. 쿼츠랑 스프링 스케줄러랑 무슨차이인가요?  -P사 다니는 K모씨 Quartz와 Spring  **Scheduler의 차이.** Quartz **\- 모든 Java 애플리케이션에 통합 할 수있는 오픈 소스 작업 스케줄 라이브러리.** **\- 수십, 수백 또는 심지어 수만 개의 작업을 실행하기위한 간단하거나 복잡한 일정을 만드는 데 사용할 수 있습니다.** \- JTA 트랜잭션 및 클러스터링 지원과 같은 많은 엔터프라이즈 급 기능이 포함되어 있습니다 Spring  **Scheduler** **- Spring은 또한 Timer와 JDK 1.3이후에  Quartz Scheduler와의 스케쥴링을 지원하기위한 통합 클래스를 제공한다.** ->[https://docs.spring.io/spring/docs/current/spring-framework-reference/html/scheduling.html](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/scheduling.html)

 참고 :

스프링 배치와 전자전부 프레임워크 : [http://open.egovframe.go.kr/nforges/information/filearchive/6179/.do](http://open.egovframe.go.kr/nforges/information/filearchive/6179/.do)

스프링 배치 이점 : [https://groups.google.com/forum/#!topic/ksug/9FMlJaE-zKU](https://groups.google.com/forum/#!topic/ksug/9FMlJaE-zKU)

스프링 배치 vs DB 프로시저 : [https://groups.google.com/forum/#!topic/ksug/vznlOZarb3s](https://groups.google.com/forum/#!topic/ksug/vznlOZarb3s)

대규모 배치시스템 성공적인 구축 전략 : [https://www.kodb.or.kr/info/info\_04\_view.html?field=&keyword=&type=techreport&page=135&dbnum=128484&mode=detail&type=techreport](https://www.kodb.or.kr/info/info_04_view.html?field=&keyword=&type=techreport&page=135&dbnum=128484&mode=detail&type=techreport)