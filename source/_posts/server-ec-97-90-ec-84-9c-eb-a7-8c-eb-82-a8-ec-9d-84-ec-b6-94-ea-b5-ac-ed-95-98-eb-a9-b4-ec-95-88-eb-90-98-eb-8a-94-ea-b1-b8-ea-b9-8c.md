---
title: 2018 세미나 - Server에서 만남을 추구하면 안되는 걸까
url: 1647.html
id: 1647
categories:
  - 공개스터디
date: 2018-04-23 20:38:10
tags:
---

강진우님의 리눅스 커널 이야기를 읽고 직접 실습을 하며 정리한 내용 입니다.

도입
==

사람과 사람사이 예의가 정말 중요 합니다. 서버에서 동작하는 서비스들 한테도 마찬가지인데요. 이 글에서는서버에서 돌아가는 서비스들의 관계와 사람의 관계를 비교해 가면서 설명 하겠습니다.

![](https://ahea.files.wordpress.com/2018/04/null.png)

1차 웹서비스
=======

![](https://ahea.files.wordpress.com/2018/04/null1.png) 예제에서 쓰인 웹 서비스는 파이썬 웹 프레임워크인 Flask를 썻으며 DB로는 Key/Value store인 redis를 사용 하였습니다.

1.  http://localhost:5000/test/1 접속
2.  Flask에서 Redis에 접속하여 Set(1,155030303)를 실행(redis에 데이터 주입)
3.  Flask에서 Redis에 Get(1)을 하여 데이터 가져오기
4.  사용자에게 전달

해당 서비스의 플로우는 총 4단계로 구성 되며 이 예제를 가지고 사용자에게 서비스 가능한 웹서비스를 튜닝 하겠습니다. 사과를 구입할 경우엔 이런식의 플로우로 흘러간다고 예를 들겠습니다. ![](https://ahea.files.wordpress.com/2018/04/null2.png) Flask와 Redis의 사이도 마찬가지인데요

![](https://ahea.files.wordpress.com/2018/04/null3.png)

위의 그림과 같이 3way-handshake 후에 데이터셋팅을 하고 데이터를 가져오며 4way-handshake를 통해 연결을 끊습니다. 만약에 이작업을 1000명 혹은 10000명이 요청할 경우 ![](https://ahea.files.wordpress.com/2018/04/null4.png) 2가지 문제점이 발생하게 됩니다.

1.  TCP 연결과 종료에 대한 오버헤드
2.  local port에 대한 고갈

여기서 잠깐!!! local port란?
----------------------

![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (1)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-1.png) ![](https://ahea.files.wordpress.com/2018/04/null5.png) Client가 TCP 소켓 연결시 필요한 port, OS에서 정의 해놓은 local port 범위 안에서 할당 보통 http Request를 날리거나 다른 TCP연결을 할시에 OS에서 ‘IP주소:로컬포트’를 가지고 통신을 하게 됨

문제점 2가지
-------

### TCP 통신 오버헤드

![](https://ahea.files.wordpress.com/2018/04/null6.png) 3way-handshake와 4way-handshake를 1000번 10000번 하게 되면 그만큼의 네트워크 자원 낭비가 되기 때문에 계속 연결 되어 있는 상태여야 합니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (9)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-9.png) 아까의 사과 구매 프로세스를 대입해 보면 사과 1000개를 구매 할 때에도 똑같이 1000번을 인사해야 하는 불상사가 생깁니다.

### local port 고갈의 문제

![](https://ahea.files.wordpress.com/2018/04/null7.png) Flask에서 Client가 되므로 지속적으로 local_port를 만들어 내어 나중엔 고갈이 되어 버리는 현상까지 오게 됩니다. 그리고 다시 사용할시엔 timewait지속 시간이 60초 이므로 60초가 지나야 다시 사용 가능 합니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c.png) netstat 명령어를 통해 현재 OS의 소켓 상태를 확인 할수 있으며 Flask가 local_port를 무지막지하게 사용한 모습을 보실수 있습니다. ![](https://ahea.files.wordpress.com/2018/04/null5.png) 우분투에서 할당 해 줄수 있는 포트를 ‘sysctl -a | grep local_port’ 명령어로 확인 할수 있습니다. 위의 문제점을 해결할 방법도 2가지가 됩니다.

1.  net.itv4.tcp\_tw\_reuse 활성화
2.  Connection Pool

해결법 2가지
-------

### net.ipv4.tcp\_tw\_reuse 활성화

![](https://ahea.files.wordpress.com/2018/04/null8.png) ‘sysctl -a | grep reuse’명령어를 통해 확인 할수 있으며 0이면 비활성 1이면 활성화 입니다. ‘sysctl -w net.ipv4.tcp\_tw\_reuse=1’명령어를 입력시 활성화 되며 이 기능은 패킷 소실의 위험이 있습니다. 지금과 같은 형태에서는 비추천 하지만 다른서버에 접속하는 Cilent용 서버일 경우에는 필요 할 수도 있습니다.

### Connection Pool

![](https://ahea.files.wordpress.com/2018/04/null9.png) 다른 해결 방법인 Connection Pool을 만들어 사용 하는 것입니다. 미리 Pool을 만들어 놓고 사용자들이 http Request를 사용시엔 ![](https://ahea.files.wordpress.com/2018/04/null10.png) 이런 식으로 3way-handshake와 4way-handshake가 사라지기 때문에 TCP 맺고 끊음에 대한 오버헤드가 사라지게 됩니다. ![](https://ahea.files.wordpress.com/2018/04/null11.png) 그리고 계속 연결 상태 이므로 로컬 포트도 고정적으로 사용하게 되어서 고갈을 방지 할 수 있습니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (10)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-10.png) 사과 구매를 할때에도 인사를 한번만 할수 있으므로 인사(TCP 연결과 종료)할 수 있으므로 오버헤드를 감소 할수 있습니다.

2차 웹서비스 Nginx를 추가 해보자
=====================

실질적으로 외부에 서비스하는 웹서비스들은 대부분 앞에 Web Server를 두게 되는데 그이유는 크게 Virtual Host, resource cache, Route등의 이유가 있어 약간의 지연이 있어도 3tier(WebServer-App-DB)로 구성을 하게 됩니다. ![](https://ahea.files.wordpress.com/2018/04/null12.png) 그런데 바로 추가시엔 위와 동일한 현상이 발생하게 됩니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (2)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-2.png) 다시 나타난 2가지 문제점

1.  TCP 연결과 종료에 대한 오버헤드
2.  local port에 대한 고갈

![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (3)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-3.png) ![](https://ahea.files.wordpress.com/2018/04/null13.png) Nginx가 Cilent이며 Flask가 Server인 입장이 되어 위와 동일한 현상이 발생하게 됩니다. 이 문제점도 동일하게 해결법이 2가지가 있습니다.

1.  net.ipv4.tcp\_tw\_reuse 활성화
2.  Keepalive Connections

Keepalive Connections 추가
------------------------

![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (4)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-4.png) nginx 설정에서 keepliave만 추가 해주면 됩니다. 자세한 설정은 기본 예제도 많으므로 nginx example을 참조해서 keepalive만 추가 해 줍시다. ![](https://ahea.files.wordpress.com/2018/04/null14.png) 위와 같은 구성도가 되며 Nginx에서 Keepalive Connections를 만들어 관리하게 됩니다. 참고로 time_out 값도 줄수 있어 한번 연결 후에는 몇초 후에 Keepalive를 없앨지 정할 수 있습니다. keepalive를 추가 할때와 안할때의 플로우는 아래와 같습니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (5)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-5.png) ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (6)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-6.png) 보통 한번 http연결시 6~8번의 요청을 하게 된다고 하여 keepalive로 TCP 연결과 종료에 대한 오버헤드를 줄일 수 있습니다. Keepalive 설정 전후의 테스트 결과 입니다. 스트레스 테스트 툴은 [siege](https://www.joedog.org/siege-home/)를 사용 하였습니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (7)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-7.png) TPS가 200이나 차이가 났습니다. ![2018 세미나 - Server에서 만남을 추구하면 안되는 걸까 (8)](https://ahea.files.wordpress.com/2018/04/2018-ec84b8ebafb8eb8298-serverec9790ec849c-eba78ceb82a8ec9d84-ecb694eab5aced9598eba9b4-ec9588eb9098eb8a94-eab1b8eab98c-8.png) netstat를 통해 테스트 후의 time_wait소켓을 살펴보니 8300개의 차이가 발생 하였습니다.

최종 정리
=====

*   서비스 대 서비스로 연결시에는 Connection Pool을 고려해 보자!
*   Nginx 사용시 Keepalive사용을 고려해 보자!
*   time_wait소켓이 많다는건 나쁘진 않지만 지나치게 많을 경우 살펴 보자!

출처

*   책 ‘리눅스 커널 이야기’ 강진우 지음
*   nginx keepalive - [https://www.nginx.com/blog/http-keepalives-and-web-performance/](https://www.nginx.com/blog/http-keepalives-and-web-performance/)
*   TCP Flags - http://www.dbguide.net/knowledge.db?cmd=view&boardUid=183652&boardConfigUid=21