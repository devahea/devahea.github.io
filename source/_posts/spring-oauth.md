---
title: Spring OAuth 적용기
tags:
  - oauth
  - spring
  - spring oauth
  - spring security
url: 27.html
id: 27
categories:
  - 미분류
date: 2017-01-18 20:29:38
---

우선 제가 테스트 하는 로컬 환경의 설정은 이렇습니다

![스크린샷 2016-08-04 오전 11.35.37.png](https://ahea.files.wordpress.com/2017/01/27_eg2m7vmx1y.png)

서버 포트는 9090이고 contextpath는 api입니다

즉 http://localhost:9090/api로 주소가 시작됩니다

테스트를 위해 해당 주소로 mapping을 만들었습니다

정상적으로 실행된다면 “Blind Interview Server Running” 이라는 문구가 나오겠죠

![스크린샷 2016-08-04 오전 11.33.57.png](https://ahea.files.wordpress.com/2017/01/27_ipim7asqkb.png)

브라우저로 실행시켜보겠습니다

![스크린샷 2016-08-04 오전 11.37.30.png](https://ahea.files.wordpress.com/2017/01/27_pf5bxtpcoz.png)

시큐리티에서 에러가 뜨네요

지금부터 시큐리티에서 token을 발급받고 발급받은 토큰으로 실행시키는 법을 확인해보겠습니다

저는 curl로 테스트를 진행하겠습니다

우선 토큰을 발급받기 위한 환경을 보겠습니다

![스크린샷 2016-08-04 오전 11.39.31.png](https://ahea.files.wordpress.com/2017/01/27_jt2vn6dbgi.png)

@EnableResourceServer와 @EnableAuthorizationServer 어노테이션을 넣었습니다

application.properties에서는 해당 설정을 넣었습니다

![스크린샷 2016-08-04 오전 11.40.41.png](https://ahea.files.wordpress.com/2017/01/27_fjifkfcfv4.png)

해당 정보가 어떻게 사용되는지는 다음을 확인하면 될것 같습니다

![스크린샷 2016-08-04 오전 11.42.18.png](https://ahea.files.wordpress.com/2017/01/27_zf7wbmfc9r.png)

아 제 해상도가 레티나라서 너무 좋아서 안보이네요

![스크린샷 2016-08-04 오전 11.45.09.png](https://ahea.files.wordpress.com/2017/01/27_qsjmla_paj.png)

아 확대했는데도 안보이네요(레티나 짱짱맨)

![스크린샷 2016-08-04 오전 11.45.53.png](https://ahea.files.wordpress.com/2017/01/27_d54e64ngor.png)

(잘보이는군요)

해당 명령을 날렸습니다

curl ahea\_client\_id:ahea\_client\_secrit@localhost:9090/api/oauth/token -d grant\_type=password -d client\_id=ahea\_client\_id -d scope=read -d username=ahea -d password=ahea123

보면

curl {security.oauth2.client.client-id}:{security.oauth2.client.client-secret}@주소/oauth/token -d grant\_type=password -d client\_id={security.oauth2.client.client-id} -d scope=read -d username={security.user.name} -d password={security.user.password}

로 날린것을 확인할 수 있습니다 ( {var}는 application.properties 키 값을 말하는겁니다}

결과를 보면

{"access\_token":"211622d1-f256-4d23-9f77-a35ed7632396","token\_type":"bearer","refresh\_token":"ae2bf389-782b-40a2-b2a0-10da4e90f252","expires\_in":42976,"scope":"read"}

이렇게 json으로 떨궈줍니다

이중 access_token값을 이용하여 api를 호출합니다 (나머지도 인증을 위해 저장해야 하는 값이니 클라이언트가 저장하고 있어야 합니다)

토큰을 이용해 api를 호출해봅니다

![스크린샷 2016-08-04 오전 11.51.52.png](https://ahea.files.wordpress.com/2017/01/27_zixjnwiuql.png)

curl -H "Authorization: Bearer 211622d1-f256-4d23-9f77-a35ed7632396" "http://localhost:9090/api/"

다음과 같이 curl명령어를 날렸습니다

규칙은 다음과 같겠죠

curl -H "Authorization: Bearer {request_token}" "{api url}"

결과를 보니 해당 api가 정상적으로 호출되었습니다

만약 curl 을 이용하여 token헤더 없이 날린다면 다음과 같은 결과가 나옵니다

![스크린샷 2016-08-04 오전 11.54.25.png](https://ahea.files.wordpress.com/2017/01/27_ebdsat6epb.png)

{"error":"unauthorized","error_description":"Full authentication is required to access this resource"}