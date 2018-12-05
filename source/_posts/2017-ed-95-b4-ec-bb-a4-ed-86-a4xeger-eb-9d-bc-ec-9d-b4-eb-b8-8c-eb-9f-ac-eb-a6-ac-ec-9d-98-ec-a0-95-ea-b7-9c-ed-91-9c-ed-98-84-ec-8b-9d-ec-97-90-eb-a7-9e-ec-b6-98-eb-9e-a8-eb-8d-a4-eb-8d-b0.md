---
title: '[2017해커톤]Xeger라이브러리의 정규표현식에 맞춘 램덤 데이터 생성'
url: 1450.html
id: 1450
categories:
  - 미분류
date: 2017-08-15 10:21:12
tags:
---

String regex = "\[ab\]{4,6}c";

Xeger generator = new Xeger(regex);

String result = generator.generate();

assert result.matches(regex);

1.  램덤 데이터를 원하는 정규표현식을 작성합니다.
2.  Xeger 객체를 만 듭니다.
3.  generate() 메서드를 통해 데이터를 만들며
4.  String의 정규표현식 판별 메소드 matches로 정규표현식에 맞는 데이터가 생성되었는지 확인 합니다.
5.  위의 예제는 자릿수 4~6자리의 a나 b의 문자로 채워지는 문자열을 만드는 예제 입니다.