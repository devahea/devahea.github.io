---
title: WebDriver VS DevTools Protocol
date: 2019-05-01 10:19:50
img: https://user-images.githubusercontent.com/6037055/56453226-305be700-637a-11e9-8142-068e4040e871.png
categories:
  - WEB
tags: 
  - HeadlessBrowser
  - Chrome
  - headless
  - javascript
author: 김종인
---

## WebDriver
![WebDriver Flow](https://user-images.githubusercontent.com/6037055/57774314-29e04580-7755-11e9-8b1b-8f7be681c8ec.png)
`Web Browser 자동화`를 위해 태어 났으며, 보통 `Cross Browser Test`와 `UI Test`에 활용 합니다. WebDriver는 [W3에서 정의한 WebDriver 스펙](https://w3c.github.io/webdriver/)에 맞춰 각 브라우저 마다 구현된 WebDriver가 있으며 Selenium WebDriver를 통해 통합적으로 다룰 수 있습니다. 아래에는 각 브라우저별 지원 드라이버 입니다.

### 지원하는 드라이버들 (2019.04.28 기준)
Browser | Maintainer | Versions Supported
--- | --- | ---
Chromium | [Chromium](https://sites.google.com/a/chromium.org/chromedriver/) | All versions
Firefox | [Mozilla](https://github.com/mozilla/geckodriver) | 54 and newer
Internet Explorer | Selenium | 6 and newer
Opera | Opera [Chromium](https://github.com/operasoftware/operachromiumdriver) / [Presto](https://github.com/operasoftware/operaprestodriver) | 10.5 and newer
Safari | [Apple](https://webkit.org/blog/6900/webdriver-support-in-safari-10/) | 10 and newer

## DevTools Protocol(Debugging Protocol)
![Edge DevTools Protocol](https://46c4ts1tskv22sdav81j9c69-wpengine.netdna-ssl.com/wp-content/uploads/mswbprod/sites/33/2018/05/b46a7b838118991ffd34a9092310ccf0-1024x501.png)
Web의 발전함에 따라 Web 개발 기술도 점점 고도화 되어가고 있습니다. 이를 원활히 지원하기 위해 각 브라우저에서는 전문적으로 디버깅을 할 수 있는 `DevTools Protocol`을 지원 합니다. 
![Chrome DevTools 지원 기능](https://user-images.githubusercontent.com/6037055/57776332-24d1c500-775a-11e9-9437-54a537e0961a.png)
`DevTools Protocol`는 Web Application을 진단할 수 있도록 Elements, Network, Perfomance, TimeLime, Trace등 다양한 기능을 지원하고 있습니다.

### 지원중인브라우저
![DevTools Protocol API 지원 현황](https://user-images.githubusercontent.com/6037055/57775898-159e4780-7759-11e9-992a-652672b50d97.png)
[브라우저별 지원 API](http://compatibility.remotedebug.org/)를 통해 사용 가능한 API를 확인 하실 수 있습니다.
아래는 DevTools Protocol 지원 브라우저 입니다.
* [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/)
* [WebKit / Safari](https://github.com/WebKit/webkit/tree/master/Source/JavaScriptCore/inspector/protocol)
* [Node.js](https://chromedevtools.github.io/devtools-protocol/v8/)
* Firefox - [in development](https://groups.google.com/forum/#!msg/mozilla.dev.platform/4-4A8W-nP5g/Y9C9UkWTAAAJ)
* [Edge DevTools Protocol](https://docs.microsoft.com/en-us/microsoft-edge/devtools-protocol/)

## 출처
* https://blogs.windows.com/msedgedev/2018/05/11/introducing-edge-devtools-protocol/
* https://seleniumhq.github.io/docs/wd.html
* https://stackoverflow.com/questions/50939116/what-is-the-difference-between-webdriver-and-devtool-protocol
* https://github.com/WICG/devtools-protocol