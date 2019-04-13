---
title: 너는 사람이냐? 봇이냐?(Headless Browser)
date: 2019-04-13 14:16:01
categories:
  - web
tags: HeadlessBrowser, Chrome, headless, javascript
---

![image](https://user-images.githubusercontent.com/6037055/56078088-da89bb00-5e1e-11e9-80f9-2f139d96e9a6.png)
Web을 통해 다양한 정보를 소비하며 쉽게 접근 할 수 있는 환경에 살고있습니다.
이 다양한 정보를 가지고 애플리케이션을 만들어 사용하고 싶은데 `Open API`를 제공하지 않아 곤란해 하는 경우가 많고 제대로 제공하지 않는 경우도 부지기수 입니다. 
그래서 사용하던것이 `HTTP Client`를 사용하여 Scraping을 하고 원하는 데이터를 처리하여 사용하고 있습니다.

```bash
# 구글 뉴스 스크래핑
curl https://news.google.com/?tab=wn&hl=ko&gl=KR&ceid=KR:ko > google_news.html
```

Web Trend의 고도화가 진행됨고 동시에 Web 개발에도 엄청난 발전이 있었는데요. `SPA(Single Page Application)`이라는 형식의 개발이 유행을 타면서 부터 많은 Site들이 SPA방식으로 제공 하고 있습니다. 

문제는 HTTP Client로 SPA를 접근할때 원하는 데이터를 가져올수 있는 상황이 생깁니다. 
그리고 로그인을 해야만 접근 가능한 Site에 로그인 인증을 어떻게 처리해야 할지 막막한 상황에 부딫히게 됩니다. 

![image](https://user-images.githubusercontent.com/6037055/56078497-c09ea700-5e23-11e9-97e7-b37f6ae199e7.png)
그래서 나오게 된게 `Headless Browser`이며, 실제 사람이 Browser를 통해 접속하는 방식으로 Scraping을 할 수 있게 되었습니다. 

Headless Browser란 무엇이며 HTTP Client와는 어떤 차이점이 있는지 Headless Browser종류와 성능에 대해 알아 볼것입니다.  


앞으로 다루어질 내용
* Headless Browser란?
* Puppeteer VS Selenium VS PhantomJS VS HttpClient
* Headless Browser 활용 예제