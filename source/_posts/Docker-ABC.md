---
title: Docker ABC
date: 2019-04-14 23:26:52
img: https://i0.wp.com/blog.docker.com/wp-content/uploads/2013/08/KuDr42X_ITXghJhSInDZekNEF0jLt3NeVxtRye3tqco.png
categories:
  - infra
tags: 
  - docker
  - container
  - linux
---
# Docker ABC

## Docker
컨테이너 기반의 오픈소스 가상화 플랫폼이다. 
Solomon Hykes가 2013년 pycon에서 발표하면서 알려졌고, 2013년 3월 오픈소스로 릴리즈되었다.
최초에는 LXC를 이용하여 리눅스 컨테이너에 종속되었는데, 2014년 3월 v0.9에서 기본 실행 환경을 libcontainer로 대체하며 LXC를 버렸다. 
당시 회사명은 dotCloud였는데 docker가 인기를 끌면서 2013년 10월 아예 회사이름을 Docker로 변경했다. 

### Container
시스템의 나머지 부분과 격리된 하나 이상의 프로세스 세트이다. OS 커널은 공유하며, 컨테이너 간에 격리하기 위해 런타임에 필요한 코드/설정/의존성 라이브러리 등 모든 것을 포함해서 패키징한다.

![](https://www.redhat.com/cms/managed-files/what-is-a-container.png)

### Virtual Machine
하이퍼 바이저를 통해 격리된 하드웨어를 제공받아, 그 위에 OS/설정/코드/의존성 라이브러리 등 모든 것을 포함한다. 

![](https://www.ionos.com/digitalguide/fileadmin/DigitalGuide/Screenshots_2018/EN-virtual-machine.png)

### Container vs Virtual Machine
| ![](https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-appliction-blue-border_2.png) | ![](https://www.docker.com/sites/default/files/d8/2018-11/container-vm-whatcontainer_2.png) |
|---|---|

컨테이너와 VM은 유사하게 '리소스 격리 및 할당'이라는 이점을 갖지만, 컨테이너가 하드웨어가 아닌 운영 체제를 가상화하기 때문에 기능이 다르게 작동한다.

- Container
  - OS 가상화 (OS를 공유)
    - OS를 공유함으로써 컨테이너는 OS 풀버전을 포함하지 않아도 되기 때문에 디스크를 적게 사용하게 된다.
    - OS를 공유함으로써 OS 실행에 필요한 기본 메모리를 사용하지 않는다.
    - OS를 공유함으로써 컨테이너는 OS 를 부팅할 필요가 없기 때문에 시작/종료가 빠르다.
    - OS를 공유함으로써 각 컨테이너는 OS 종속성이 생긴다.
  - 이미지를 레이어단위로 관리
    - 공통 레이어를 공유함으로써 디스크를 적게 사용하게 된다.
    - app 배포시 기존 레이어에 증분 배포하기 때문에 빠르다.
- Virtual Machine
  - Hardware 가상화 (Hardware를 공유)
    - Hardware 위에 OS 설치를 위해 OS 풀버전을 포함해야 하기 때문에 디스크를 많이 사용하게 된다.
    - OS가 기본적으로 CPU/Memory와 같은 자원을 일정량 필요로 하기 때문에 자원 손실이 있다.
    - Hardware 만을 공유하기 때문에 각 VM는 서로 다른 OS를 구축할 수 있다.
    - VM 단위 배포시 OS 부팅부터 시작하기 때문에 속도가 느릴 수 있다.

## How?
### cgroups (Control groups)
프로세스들의 자원의 사용(CPU, 메모리, 디스크 입출력, 네트워크 등)을 제한하고 격리시키는 리눅스 커널 기능.
![](https://jaxenter.com/wp-content/uploads/2018/01/java-in-containers-9.png)

### namespaces

쉽게 생각하면 cgroups가 quantity라면 namespaces는 quality 개념이다.

> 이 섹션에서는 리눅스 커널의 기능은 다루지 않을 예정입니다.

앞으로 다루어질 내용
- Why? (왜 Docker를 사용하는가?)
- Docker network
- Docker image
- Dockerfile


[Why do we use a OS Base Image with Docker if containers have no Guest OS?](https://serverfault.com/questions/755607/why-do-we-use-a-os-base-image-with-docker-if-containers-have-no-guest-os)

[Understanding Docker "Container Host" vs. "Container OS" for Linux and Windows Containers](http://www.floydhilton.com/docker/2017/03/31/Docker-ContainerHost-vs-ContainerOS-Linux-Windows.html)
