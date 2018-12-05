---
title: '[2017 해커톤]SqlBuilder를 이용하여 SqlExport 개발'
url: 1485.html
id: 1485
categories:
  - 해커톤
date: 2017-08-15 10:33:00
tags:
---

![캡처](https://ahea.files.wordpress.com/2017/08/ecbaa1ecb2983.png) Map을 SQL로 변환하기 위해 SqlBuilder를 사용하였습니다. 1\. 테이블이름을 파라미터로한 InsertQuery 생성자를 이용하여 객체를 생성합니다. 2\. 반복문을 돌며 addCustomColumn(String, String) 이용하여 column과 value값을 넣어줍니다.

 ex ) INSERT INTO table_name(column1) VALUES(value1), INSERT INTO table_name(column1, column2) VALUES(value1, value2), …

3\. validate()는 쿼리의 열과 테이블이 실제로 의미가 있는지를 확인해줍니다. 참고문헌 \- http://openhms.sourceforge.net/sqlbuilder/index.html