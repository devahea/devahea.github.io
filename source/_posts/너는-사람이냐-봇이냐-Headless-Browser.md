---
title: 너는 사람이냐? 봇이냐?(Headless Browser)
date: 2019-04-13 14:16:01
img: https://user-images.githubusercontent.com/6037055/56453226-305be700-637a-11e9-8142-068e4040e871.png
categories:
  - WEB
tags: 
  - HeadlessBrowser
  - Chrome
  - headless
  - javascript
---

![image](https://user-images.githubusercontent.com/6037055/56078088-da89bb00-5e1e-11e9-80f9-2f139d96e9a6.png)
Web을 통해 다양한 정보를 소비하며 쉽게 접근 할 수 있는 환경에 살고있습니다.
이 다양한 정보를 가지고 애플리케이션을 만들어 사용하고 싶은데 `Open API`를 제공하지 않아 곤란해 하는 경우가 많고 제대로 제공하지 않는 경우도 부지기수 입니다. 

![구글 뉴스 헤드라인](https://user-images.githubusercontent.com/6037055/56088150-d78dd980-5eb5-11e9-9570-2ac8b5a3987e.png)
```bash
# 구글 뉴스 스크래핑
curl https://news.google.com/?tab=wn&hl=ko&gl=KR&ceid=KR:ko > google_news.html
```
```html
# google_news.html 파일 내용중 일부
<h3 class="ipQwMb ekueJc RD0gLb">
    <a href="./articles/CAIiEBTOk3KYcU6RvQ0CfhFY0VUqGQgEKhAIACoHCAow2t-aCDDArqABMNST5AU?hl=ko&amp;gl=KR&amp;ceid=KR%3Ako" class="DY5T1d" >
        부산 변호사 58명 이미선 후보자 임명해야
    </a>
</h3>
```
그래서 사용하던것이 `HTTP Client`를 사용하여 Scraping을 하고 원하는 데이터를 처리하여 사용하고 있습니다.

![javascript-Javascript](https://user-images.githubusercontent.com/6037055/56453083-297fa500-6377-11e9-9765-361264370b7f.png)
Web Trend의 고도화가 진행됨과 동시에 Web 개발에도 엄청난 발전이 있었는데요. `SPA(Single Page Application)`이라는 형식의 개발이 유행을 타면서 부터 많은 Site들이 SPA방식으로 제공 하고 있습니다. 

![javascript-NoJavascript](https://user-images.githubusercontent.com/6037055/56453086-2e445900-6377-11e9-8727-dccd79463cf2.png)
문제는 HTTP Client로 SPA에 접근할때 Javascript를 실행 하지 못해 원하는 데이터를 가져올수 없는 상황이 생깁니다. 
그리고 로그인을 해야만 접근 가능한 Site에 로그인 인증을 어떻게 처리해야 할지 막막한 상황에도 부딫힐 수도 있고 HTTP Client로는 한계가 있어 제대로 Scraping 하기 힘들어 집니다. 

![HeadlessBrowser](https://user-images.githubusercontent.com/6037055/56453226-305be700-637a-11e9-8142-068e4040e871.png)
그래서 나오게 된게 `Headless Browser`이며, 실제 사람인지 봇인지 분간이 안될정도로 Headless Browser를 통해 접속하는 방식으로 Scraping을 할 수 있게 되었습니다.

Headless Browser란 무엇이며 HTTP Client와는 어떤 차이점이 있는지 Headless Browser종류와 성능에 대해 알아 볼것입니다.  

> 이 섹션에서는 검색 엔진 봇은 다루지 않을 예정 입니다.

앞으로 다루어질 내용
* Headless Browser란?
* Puppeteer VS Selenium VS PhantomJS VS HttpClient
* Headless Browser 활용 예제