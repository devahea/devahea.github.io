---
title: '[2017해커톤] Spring Boot / MariaDB 설정'
url: 1478.html
id: 1478
categories:
  - 미분류
date: 2017-08-15 10:27:37
tags:
---

\[해커톤\] Spring Boot / MariaDB 설정
================================

pom.xml에 다음과 같이 설정 \[code lang=text\] <dependency> <groupId>mysql</groupId> <artifactId>mysql-connector-java</artifactId> <scope>runtime</scope> </dependency> <dependency> <groupId>org.mariadb.jdbc</groupId> <artifactId>mariadb-java-client</artifactId> <version>1.1.7</version> </dependency> \[/code\] application.properties에 다음과 같이 설정 \[code lang=text\] <br />spring.datasource.platform=mysql spring.datasource.url=jdbc:mariadb://192.168.219.154:3306/ahea spring.datasource.username=root spring.datasource.password=0000 spring.datasource.driverclassName=org.mariadb.jdbc.Driver \[/code\]