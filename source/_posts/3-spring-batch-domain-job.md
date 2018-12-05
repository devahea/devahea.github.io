---
title: 3. Spring batch domain - job
url: 346.html
id: 346
categories:
  - spring batch
date: 2017-02-17 17:45:17
tags:
---

아래 보이는 다이어그램은 수십년간 사용된 Batch 아키텍쳐의 단순화된 버전입니다.

이것은 배치처리과정에 대한 domain 을 대략적으로 구성해서 보여줍니다. 스프링 배치는 견고하고 유지보수가 용이한 구현을 제공합니다.

![image](https://ahea.files.wordpress.com/2017/02/image7.png) JobLauncher로 작업을 실행하고 현재 실행중인 프로세스에 대한 메타 데이터를 JobRepository에 저장합니다.  

**1\. Job**

Job 은 배치 프로세스에서 캡슐화 되있는 entity이고 단순히 Step을 위한 컨테이너입니다. 

projet에서 job은 xml이나 java base 컨피그 설정을 할 수 있습니다.

![image1](https://ahea.files.wordpress.com/2017/02/image11.png)

**JobInstance**

다이어그램의 'EndOfDay'작업과 같이 하루가 끝날 때 한 번 실행해야하는 배치 작업을 생각해보면, 하루에 하나의 'EndOfDay'작업이 존재하고, 이것은 1 월 1 일 및 1 월 2 일이 실행됩니다. JobInstance는 실패했을때 여러번 돌릴 수 있으며, 성공시 생명주기가 끝나게 됩니다.

이미 완료된 JobInstance를 돌리려고 한다면 에러를 발생시킵니다.

**JobExecution**

`JobExecution`**은 한번 시도 되는 job의 개념입니다. **EndOfDay 을 예로 들면 만약 01-01-2008에 돌리는 Job을 실패했을때, 같은 파라미터(01-01-2008 )로  돌리면 새로운 `JobExecution` 가 생성됩니다. 그러나 `JobInstance` 은 여전히 하나입니다.

![image3](https://ahea.files.wordpress.com/2017/02/image31.png)

JobExecution 의 코드를 보면 상태, 시작시간, 종료시간이 있고 기타 필요한것들이 들어 있습니다.

정리하면 Job과 JobParameter가 JobInstance를 만들고 이게 실행되는데 실행되는 시도가 JobExecution1 입니다.

이때 JobInstance가 실패해서 한번 더 시도 하면 JobExecution2 가 생기고 JobExecution에 위에와 같은 메타 정보가 저장됩니다.

Job 을 등록하는 예제입니다.

@Bean

`    public Job job() { return jobBuilderFactory.get(JOB_NAME) .start(StepName) .build();`    }   **JobBuilderFactory 는 get으로 builder를 얻어오고 job repository를 초기화 시킵니다. ** ![image4](https://ahea.files.wordpress.com/2017/02/image41.png)

JobBuilder는 start를 호출하고 step 이름을 넘겨서 SimpleJobBuilder를 호출함.

step을 호출할때  fluent method chaining이 됩니다.

![image5](https://ahea.files.wordpress.com/2017/02/image51.png) SimpleJobBuild는 build를 호출하면서 job에 Steps을 set하고 job을 리턴합니다. ![image6](https://ahea.files.wordpress.com/2017/02/image61.png)

**2\. Step**

** 모든 Job은 하나 이상의 단계로 이루어져 있습니다. Step에는 실제 배치 처리를 정의하고 제어하는 데 필요한 모든 정보가 들어 있습니다.**

Step Execution 하나의 step을 실행하는 한번의 시도. 시작시간, 종료시간,상태, 종료상태, commitCount, itemCount 의 속성을 가지고, Job 과 Step 그 연관관계와 개념은 가이드 소스를 보면서 진행하도록 하겟습니다. 참고 : http://open.egovframe.go.kr/nforges/information/filearchive/6179/.do ![%ec%a0%95%eb%a6%ac](https://ahea.files.wordpress.com/2017/02/eca095eba6ac.png)