---
title: maven 외부 jar 추가
url: 16.html
id: 16
categories:
  - maven
date: 2017-01-11 10:18:09
tags:
---

maven을 이용해서 개발하다보면 알아서 라이브러리를 관리해 줘서 편하지만 메이븐 중앙 리파지토리에서 포함 안된 외부 jar를 포함 시켜야 할때가 있다.

다른 회사들과 모듈연동이나 암호화할때 jar파일만 받아서  그 api를 호출하는

방식일때 사용된다.

그럼 외부에서 제공해주는 jar 파일을 메이븐 프로젝트에 추가 시켜줘야하고

회사의 사설 리파지토리가 있으면 그쪽에 jar를 넣어주고 dependency를 추가하면

별다른 설정없이 외부 jar를 사용가능하다.

그렇게 하지 못한다면 프로젝트내 특정디렉토리를 리파지토리로 잡고

그 안에 외부 jar를 넣으면 된다.

pom.xml에

             in-project

             custom jars

             file://${project.basedir}/external

이런식으로 선언해주면 project.basedir 경로를 리파지토리로 등록한다.

리파지토리 아래에 폴더명은 다음과 같은 컨밴션을 따른다.

/groupId/artifactId/version/artifactId-verion.jar

groupId = org.mockito

artifactId = mockito-all

version = 1.9.0

the library file =  mockito-all-1.9.0.jar

    org.mockito

    mockito-all

    1.9.0

![](http://cfile25.uf.tistory.com/image/2545A14457ABE6B606DC3A)

리파지토리가 잘 잡히고 경로가 컨벤션에 따라 잘 설정되었다면

메이븐이 프로젝트내 리파지토리를 잡아서 해당 jar파일을 dependency 해줄 수 있다.

단순 참조를 위해 다음과 같이 쓸 수 도 있는데

 system

    ${project.basedir}/custom.jar

 scope 의 system 이기 때문에 maven 빌드 시 해당 jar 파일이 포함되지 않는다.

system - system 스코프는 provided와 비슷하다. provide는 컴파일 시점에는 필요

하지만, 배포할 때 포함할 필요는없다. jar파일을 제공해야하고 스코프의 jar파일은

포함이 안될수도 있다.