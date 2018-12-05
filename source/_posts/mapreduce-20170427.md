---
title: MapReduce (20170427)
url: 727.html
id: 727
categories:
  - 미분류
date: 2017-04-27 10:25:33
tags:
---

이번 글은 MapReduce에 대해 자세히 공부해볼까합니다. HDFS는 파일을 저장하고, 읽고, 삭제하고, 관리하는 시스템이니 설정 및 서버 환경에 대한 이슈를 제외하면 큰 문제가 되는건 없다고 생각합니다. 하지만, MapReduce는 우리가 직접 개발해야 하는 사항이니, 자세히 알아둘 필요가 있다고 생각합니다. MapReduce가 하는 일은 비교적 간단하다.

1\. 저장된(HDFS) 파일을 key-value 쌍으로 분할하여 Mapper 함수로 전송
2\. Mapper는 key-value 쌍을 새로운 객체로 합성
3\. shuffle 과 sort
4\. Reducer는 key-value 쌍을 최종 결론으로 합성
5\. 결과를 HDFS에 저장

더 간단하게 정리하면, 저장된 파일을 파싱하여 key-value로 만들고(Mapper), 만들어진 key-value를 합치는(Reducer) 작업이다. Hadoop을 설치하려면 설정하는데 너무 오랜 시간이 걸리므로 cloudera와 hortonworks에서 가상머신으로 간단하게 실행할수 있도록 해둔 것이 있으니 다운 받아 실행하면 될것 같습니다. 전 hortonworks로 하였습니다( [https://ko.hortonworks.com/downloads/#sandbox](https://ko.hortonworks.com/downloads/#sandbox) ) sandbox를 통해 실행한 hadoop의 버전은 다음과 같습니다. \[root@sandbox study\]# hadoop version Hadoop 2.7.3.2.5.0.0-1245 Subversion git@github.com:hortonworks/hadoop.git -r cb6e514b14fb60e9995e5ad9543315cd404b4e59 Compiled by jenkins on 2016-08-26T00:55Z Compiled with protoc 2.5.0 From source with checksum eba8ae32a1d8bb736a829d9dc18dddc2 This command was run using /usr/hdp/2.5.0.0-1245/hadoop/hadoop-common-2.7.3.2.5.0.0-1245.jar input file ![hdfs_file01](https://ahea.files.wordpress.com/2017/04/hdfs_file01.png)![hdfs_file02](https://ahea.files.wordpress.com/2017/04/hdfs_file02.png) library list

commons-cli-1.2.jar
commons-collections-3.2.2.jar
commons-configuration-1.6.jar
commons-io-2.4.jar
commons-lang-2.6.jar
commons-lang3-3.3.2.jar
commons-logging-1.1.3.jar
guava-11.0.2.jar
hadoop-auth-2.7.3.2.5.0.0-1245.jar
hadoop-common-2.7.3.2.5.0.0-1245.jar
hadoop-hdfs-2.7.3.2.5.0.0-1245.jar
hadoop-mapreduce-client-app-2.7.3.2.5.0.0-1245.jar
hadoop-mapreduce-client-common-2.7.3.2.5.0.0-1245.jar
hadoop-mapreduce-client-core-2.7.3.2.5.0.0-1245.jar
hadoop-mapreduce-client-hs-2.7.3.2.5.0.0-1245.jar
hadoop-mapreduce-client-jobclient-2.7.3.2.5.0.0-1245.jar
hadoop-mapreduce-client-shuffle-2.7.3.2.5.0.0-1245.jar
hadoop-yarn-server-tests-2.7.3.2.5.0.0-1245.jar
log4j-1.2.17.jar
slf4j-api-1.7.21.jar

*   import list

![import_list](https://ahea.files.wordpress.com/2017/04/import_list.png)

*   Mapper

![Mapper](https://ahea.files.wordpress.com/2017/04/mapper.png)

*   Reducer

![Reducer](https://ahea.files.wordpress.com/2017/04/reducer1.png)

*   Main

![Main](https://ahea.files.wordpress.com/2017/04/main.png) 위 코드는 MapReduce의 기본 틀이며, 흔히 MapReduce의 Hello World라고 말하는 WordCount 코드 입니다. 실행은 Hadoop 1.x 버전에선 java -jar 로 실행이 가능했다. 하지만, Hadoop 2.x 버전이 되면서 Yarn 이라는 시스템이 생기면서 MapReduce는 컴퓨팅을 위한 프로그램만 제공하고, YARN에서 클러스터의 리소스 관리, 장애 관리 등을 하게 되었다. 그러므로 실행 방법은 hadoop 명령어를 통해서 MapReduce를 실행 한다. (YRAN 참고 : [http://www.popit.kr/what-is-hadoop-yarn/](http://www.popit.kr/what-is-hadoop-yarn/))

hadoop jar ./WordCount.jar input\_file\_path output\_file\_path

이제 출력된 로그로 분석을 해볼까 합니다. 하지만.... 기본 설정에서 실행을 했을때 System.out.println(); 에 대한 출력이 나오지 않습니다.해당 로그를 보는 방법은 총 3가지가 있습니다.

1.  JobTracker 페이지
    *   해당 방법은 yarn이 생기기 전에도 사용하던 방법입니다.
    *   Hadoop이 정상적으로 설치가 되면 기본적으로 확인하는 페이지는 NameNode UI(기본포트 : 50070)와 JobHistory(기본포트 : 19888) 입니다.
    *   이 중 JobHistory에서 확인이 가능합니다.
2.  yarn 명령어
    *   yarn logs -applicationId application_ID
    *   이를 통해 로그를 알수 있지만, application id 를 알아야하며 하나의 job에 여러 application id 가 존재하므로 확인하기 힘듭니다.
3.  MapReduce 설정 변경
    *   MapReduce의 설정 파일 중 mapred-site.xml을 수정하는 방법입니다.
    *   mapreduce.framework.name의 기본 값은 yarn 으로 되어 있는데 이를 local 로 변경하게 되면 hadoop jar 명령어를 실행함에 있어 문제 없이 System.out.println 의 출력 값을 볼수 있습니다.
    *   하지만, 이는 MapReduce의 restart를 의미하며, 그 외에도 yarn과 hive등의 프로세스를 restart해야하는 상황이 되므로 주의해야합니다.

저는 가상 머신으로 실행하고 있고 테스트 및 스터디를 위한 Hadoop이므로 설정을 바꾸는 3번 방법으로 하도록 하겠습니다.

코드 실행
-----

이렇게 실행하여 로그를 보면 Map이 실행되기 직전에

##### ![MR_log1.png](https://ahea.files.wordpress.com/2017/04/mr_log11.png)

이 있습니다.

1.  mapred.MapTask: Processing split: hdfs://sandbox.hortonworks.com:8020/user/admin/data/input/file02:0+27
    *   이 로그는 현재 Mapper 함수에서 읽은 파일입니다.
    *   MapReduce가 파일을 가장 먼저 읽는 것이 이름 역순인지 가장 최근에 업로드된 파일인지 그냥 궁금해서 알아보니 가장 최근에 업로드된 파일 순으로 읽고 있다는걸 확인했습니다.
2.  mapreduce.task.io.sort.mb: 64
    *   map을 거치고 reduce가 구동되는 시점에 data-sorting이 일어나는데 이때 사용될 파일핸들 개수 및 메모리 용량을 설정하는 부분입니다.

이제 코드 상에서 찍은 로그를 보도록 하겠습니다.

1.  Mapper
    *   ##### ![MR_log2](https://ahea.files.wordpress.com/2017/04/mr_log2.png)
        
    *   위 로그는 우리가 실제로 HDFS에 업로드한 file02입니다. value는 file의 값을 읽었으니 첫 라인이 들어가는 것은 당연한데, key값이 무엇일지 몰라 로그를 보면서 추측을 해봤습니다.
        
        ##### mapred.MapTask: bufstart = 0; bufvoid = 67108864
        
        위에 적은 로그 중 이 부분에 bufstart라는 것이 있습니다. 또한, bufvoid가 있으며, Reduce 실행시 bufend가 있습니다. 이에 대한 내용은 찾아도 알수가 없어서 [org.apache.hadoop.mapred.MapOutputBuffer](https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1/src/mapred/org/apache/hadoop/mapred/MapTask.java)를 보니 해당 변수들을 다음과 같이 설명하고 있습니다.
        
        // k/v accounting
        private volatile int kvstart = 0;  // marks beginning of spill
        private volatile int kvend = 0;    // marks beginning of collectable
        private int kvindex = 0;           // marks end of collected
        private final int\[\] kvoffsets;     // indices into kvindices
        private final int\[\] kvindices;     // partition, k/v offsets into kvbuffer
        private volatile int bufstart = 0; // marks beginning of spill
        private volatile int bufend = 0;   // marks beginning of collectable
        private volatile int bufvoid = 0;  // marks the point where we should stop
                                               // reading at the end of the buffer
        
        그러므로 Map에서 key라고 하는건 bufstart가 아닐까 하는 생각이 들었습니다. 아직 확실한 뭔가를 찾진 못했네요...
2.  Reducer...?
    *   ##### ![MR_log3](https://ahea.files.wordpress.com/2017/04/mr_log3.png)
        
    *   아직 파일을 다 읽지 않은 상황에서 Reducer가 실행이 됐습니다.
    *   이는 코드 중 job.setCombinerClass(IntSumReducer.class); 으로 인해 실행되는 Reducer 입니다. 해당 코드를 사용하지 않아도 문제는 정상 처리됩니다.
    *   하지만 만약, 용량이 크고 많은 수의 파일을 처리하며, Reducer의 key의 수가 많아진다면, Reducer의 부하가 심해지므로 각 Mapper마다 Reducer를 실행하는 것이 효율이 좋습니다.
    *   이 Reducer에서 발생되는 또 하나의 기능이 있습니다. 바로 Sort & Shuffle 입니다. key가 출력된 순서를 보면 Mapper에서 제일 먼저 만든 "Hello"가 나오지 않고 "Goodbye"가 나오고 있습니다.
3.  2번째 파일(file01)에 해당하는 Mapper & Reducer
    *   ![MR_log4.png](https://ahea.files.wordpress.com/2017/04/mr_log4.png)
    *   기본적으로 실행되는건 file02때와 같습니다. 맨 마지막에 Reduce task를 기다리는 것으로 Mapper의 일은 모두 끝이 납니다.
4.  Reducer
    *   2번째 파일(file01)까지 위와 같이 처리가 되었다면, 이제 두 Mapper의 결과를 합치는 과정이 남았습니다. 진짜 Reducer입니다.
    *   ##### ![MR_log5.png](https://ahea.files.wordpress.com/2017/04/mr_log51.png)
        
    *   Reducer 가 실행 되면서 Mapper의 결과를 가져오게 되는데 그 부분에 해당하는 로그는 위와 같이 나옵니다.
    *   ![MR_log6.png](https://ahea.files.wordpress.com/2017/04/mr_log6.png)
    *   setCombinerClass 과 setReduceClass가 같긴 때문에 실행되는것 역시 같습니다.
5.  최종 결과
    *   ![MR_log7.png](https://ahea.files.wordpress.com/2017/04/mr_log71.png)
    *   맨 마지막으로 최종적인 결과가 정리되어 나오게 되는데 실제 사용할때는 이것만 봐도 충분하다고 생각합니다. Map과 Reduce의 input/output에 대한 라인 수를 포함한 결과가 모두 나와있기 때문입니다.
    *   ![MR_result.png](https://ahea.files.wordpress.com/2017/04/mr_result.png)
6.  추가 사항
    *   Mapper 와 Reducer에서 Iterable과 Context에 대해 출력해본 결과, key의 수가 몇개든 같은 객체를 사용하고 있다는 것을 알수 있습니다. (Mapper의 확인은 input file 하나에 여러 라인으로 테스트 하면 같은 객체를 사용하고 있다는것을 알수 있습니다.)

최종 정리 ![WordCount_MapReduce_Paradigm.PNG](https://ahea.files.wordpress.com/2017/04/wordcount_mapreduce_paradigm.png) 그림 참고 : [https://wikis.nyu.edu/display/NYUHPC/Big+Data+Tutorial+1%3A+MapReduce](https://wikis.nyu.edu/display/NYUHPC/Big+Data+Tutorial+1%3A+MapReduce) 위 그림은 위키에 올라와 있는 그림입니다. Main 함수에 있는 FileInputFormat.addInputPath( job, new Path(String) ); 를 통해 Mapper에서 처리할 파일을 설정하고, 파일 내용을 라인 별로 ETL 처리 합니다.(코드 실행 1번) 실행된 각 Mapper의 결과를 Sort & Shuffle을 실행하고(코드 실행 2번), Reducer를 통해 최종 결과를 만들어냅니다.(코드 실행 3번) 만들어진 결과는 FileOutputFormat.setOutputPath( job, new Path(String) )에 설정된 HDFS에 저장 됩니다.