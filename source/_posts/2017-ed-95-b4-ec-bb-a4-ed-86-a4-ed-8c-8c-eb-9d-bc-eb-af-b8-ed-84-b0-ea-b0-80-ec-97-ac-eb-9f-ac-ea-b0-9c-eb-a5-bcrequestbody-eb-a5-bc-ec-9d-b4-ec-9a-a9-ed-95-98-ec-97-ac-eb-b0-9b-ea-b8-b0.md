---
title: '[2017해커톤] 파라미터가 여러개를@RequestBody를 이용하여 받기'
url: 1496.html
id: 1496
categories:
  - 해커톤
date: 2017-08-15 10:51:56
tags:
---

이슈 : request에서 RequestBody 요청 받을 파라미터가 여러개.

@RequestBody - body에 있는 데이터를 읽는 부분.

The body of the request is passed through an [HttpMessageConverter](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/converter/HttpMessageConverter.html "interface in org.springframework.http.converter") to resolve the method argument depending on the content type of the request.

컨트롤러에서 파라미터 마다 requestBody를 써줬는데 requestbody는 하나 이상 쓸 수가 없음. 그래서 해결책은 VO를 하나 만들어서 그 안에 필요한 파라미터들을 집어 넣음.

![무제.jpeg](https://ahea.files.wordpress.com/2017/08/e18486e185aee1848ce185a6.jpeg)

컨트롤러 테스트를 위해 json형식으로 넘겨줘야했는데, 내가 만든 객체에 맞는 json형식을 만드는데 어려웠음. 하나하나 만들고 실패하다가 방법을 찾은게 gson을 사용해서 객체를 넘기면 json형으로 나와서 테스트 객체를 만들고 그것을 gson.toJson을 사용해서 테스트 함.

나오는 형식대로 controller에서 테스틀하니 잘 들어가는것을 확인 함.

gson에 대한 간단한 설명은 A Java serialization/deserialization library to convert Java Objects into JSON and back.

[https://github.com/google/gson](https://github.com/google/gson) 에 잘 나와 있다.