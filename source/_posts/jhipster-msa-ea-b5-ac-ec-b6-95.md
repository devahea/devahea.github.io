---
title: JHipster MSA 구축
tags:
  - docker
  - java
  - JHipster
  - spring boot
url: 1734.html
id: 1734
categories:
  - 미분류
date: 2018-08-13 14:42:41
---

개요
==

Mricroservice는 수많은 장점을 가지고 있지만 구축하는건 생각보다 쉽지 않습니다. JHipster와 함께 MSA구축을 하면 한층 쉽게 다가갈수 있다고 생각 되어 글을 작성 하게 되었습니다. 본 글에서는 크게

*   개발 MSA 환경 구축
*   Docker Compose를 활용한 Service Mash

나뉘며 프로젝트 생성부터 Microservice Gateway와 Microservice Application생성과 더불어 상용 배포를 위한 Docker Compose 설정까지 알아 보겠습니다.

개발 MSA 환경 구축
============

![MSA diagram](https://www.jhipster.tech/images/microservices_architecture_detail.001.png)

*   Gateway 생성
    *   Registry 실행
    *   Gateway 실행
*   Application 생성
    *   Application 실행

여기에선 이 구조로 프로젝트를 생성 해 보겠습니다. ![wallet](https://user-images.githubusercontent.com/6037055/44248354-066b8700-a225-11e8-8303-4049bb6fc16b.png) ![directory](https://user-images.githubusercontent.com/6037055/43988352-c1192640-9d6e-11e8-9723-d77ee7674fb1.png)

Gateway 생성
----------

Gateway를 생성한 뒤 먼저 JHipster Registry를 실행한다. \[code lang=bash\] // Gateway로 프로젝트 생성 $ cd gateway $ jhipster \[/code\] ![gateway_generate](https://user-images.githubusercontent.com/6037055/43988345-bfed690c-9d6e-11e8-9c47-0fb1dd3b1516.png) \[code lang=bash\] // Registry 실행 $ docker-compose -f src/main/docker/jhipster-registry.yml up \[/code\] ![registry_run](https://user-images.githubusercontent.com/6037055/43988350-c0b8cbce-9d6e-11e8-86b4-98b644cfc333.png) JHipster Registry는 `Spring Cloud Config`와 `Eureka Server`로 이루어져 있으며 Microservice의 Config관리 및 서버 발견 역할을 한다. registry가 셋팅 된후에 gateway를 실행 시킨다. \[code lang=bash\] // Gateway 실행 $ ./gradlew \[/code\] ![gateway_run](https://user-images.githubusercontent.com/6037055/43988346-c021a9ba-9d6e-11e8-9ad7-e797411cdbb9.png)

Application 생성
--------------

\[code lang=bash\] // Application로 프로젝트 생성 (member, wallet 각각 실행) $ cd member $ jhipster \[/code\] ![member_generate](https://user-images.githubusercontent.com/6037055/43988348-c05189b4-9d6e-11e8-8b74-86da2dd6425a.png) ![wallet_generate](https://user-images.githubusercontent.com/6037055/43988351-c0eb1688-9d6e-11e8-90e8-0d0a7a825fe9.png) 생성 후 별도의 비즈니스로직 추가 후 애플리케이션을 실행 하시면 됩니다. \[code lang=bash\] // Gateway 실행 $ ./gradlew \[/code\]

개발 MSA 환경 실행
------------

지금까지 잘 따라오셨으면

*   Registry
*   Gateway
*   Application

3개의 서비스들이 잘 실행 되어 있고 Gateway에서 각각의 Application의 API를 호출하면 정상적으로 동작하는걸 확인 하실 수 있습니다. ![registry_check](https://user-images.githubusercontent.com/6037055/43988349-c08271aa-9d6e-11e8-9e30-80feec06d61a.png) ![gateway_check](https://user-images.githubusercontent.com/6037055/43988355-c1a150f6-9d6e-11e8-8c77-b38491c25a79.png) ![gateway_app_check_1](https://user-images.githubusercontent.com/6037055/43988353-c1499aaa-9d6e-11e8-9a1f-ad028fc7361b.png) ![gateway_app_check_2](https://user-images.githubusercontent.com/6037055/43988354-c17b96e0-9d6e-11e8-831e-49487bc1052d.png) swagger를 통해 Gateway에서 각각의 member와 wallet Service의 API를 확인하고 개발 할 수 있습니다.

Docker Compose를 활용한 Service Mash
================================

![docker-compose logo](https://ahea.files.wordpress.com/2018/08/d160f-1jk4vdnsrf6ynab2nyhmsdq.png) Docker Compose는 Multi-Container 정의 툴로써 YAML형식의 파일로 작성 하며, 여러 개의 Docker Container를 한번에 생성 하실 수 있습니다. Docker Compose를 실행하기전 각각의 애플리케이션의 Dokcer Image가 필요하여 아래의 명령어를 실행 해줘야 합니다. \[code lang=bash\] $ ./gradlew bootWar -Pprod buildDocker \[/code\] `docker images`로 Docker Image를 확인 하실 수 있습니다. \[code lang=bash\] $ docker images \[/code\] ![docker_images](https://user-images.githubusercontent.com/6037055/43988576-e06f3f48-9d73-11e8-97e9-42f814005407.png) 필요한 모든 준비가 완료 되었습니다. 이제 docker-compose로 서비스들을 묶은 뒤 실행만 하면 됩니다. \[code lang=bash\] $ cd wallet-app-compose $ jhipster docker-compose \[/code\] ![docker-compose_generate](https://user-images.githubusercontent.com/6037055/43988578-e25a1c4c-9d73-11e8-9d9b-86288ad38cb7.png) 생성을 완료 한 후 실행을 해봅니다. \[code lang=bash\] $ docker-compose up -d \[/code\] ![docker-compose_up](https://user-images.githubusercontent.com/6037055/43988579-e2fdcd2e-9d73-11e8-810f-423e7461d370.png) ![docker-compose](https://user-images.githubusercontent.com/6037055/44254727-a08bf900-a23e-11e8-9a75-167e6ba44e5b.png) 이외 compose사용시 유용한 명령어들 입니다.

*   `docker-compose up -d` \- 컨테이너들 생성과 동시에 실행
*   `docker-compose down` \- 컨테이너들 종료와 동시에 삭제
*   `docker-compose start` \- 컨테이너들 실행
*   `docker-compose stop` \- 컨테이너들 종료

docker로 실행 시 몇가지 유용한 명령어를 정리 해 보았습니다.

*   `docker stats` \- 컨테이너의 cpu, memory, I/O 등
*   `docker images` \- 이미지 확인
*   `docker ps` \- 컨테이너 확인
*   `docker rmi` \- 이미지 삭제
*   `docker rm` \- 컨테이너 삭제

![docker_stats](https://user-images.githubusercontent.com/6037055/43988577-e0bbab3a-9d73-11e8-8d1e-733ba811f815.png) 자세한 사용법은 명령어 뒤에 `--help` 를 넣어 확인 바랍니다. JHipster Console도 설치 하여 MSA관리를 수월할 수 있게 도와 줍니다. ![console_dashboard](https://user-images.githubusercontent.com/6037055/43988583-efa2428a-9d73-11e8-9dcf-a49e5cee3901.png) ![console_log](https://user-images.githubusercontent.com/6037055/43988604-4267cec2-9d74-11e8-94e0-ad09e3a3b3b2.png)