---
title: Selenium VS Puppeteer VS HTTP Client
date: 2019-05-01 10:19:51
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

Selenium VS Puppeteer VS HTTP Client 대해 알아볼 예정 입니다.
각각의 특징은 무엇이며, 마지막엔 비교를 하겠습니다.

### Selenium
![Selenium HomePage](https://user-images.githubusercontent.com/6037055/57007275-527b2200-6c22-11e9-9ec6-3938285bbc67.png)

Selenium이란 `Web Browser 자동화`를 위해 태어 났으며, 보통 `Cross Browser Test`에 활용

Selenium 에서 오픈 중인 프로젝트는
* Selenium-WebDriver : 로컬 또는 원격 시스템에서 브라우저를 기본적으로 구동 할 수 있습니다.
* Selenium-Grid : 여러 서버에서 동시에 여러 테스트를 실행하여 여러 브라우저 나 운영 체제를 테스트하는 데 걸리는 시간을 줄여 Selenium Remote Control을 다른 수준으로 끌어 올립니다.
* Selenium IDE : 브라우저에서 테스트를 기록하고 재생할 수있게 해주는 Chrome 및 Firefox 확장 기능입니다.
* Selenium Remote Control : 거의 모든 프로그래밍 언어와 테스트 프레임 워크를 사용하여 웹 브라우저를 로컬 또는 다른 컴퓨터에서 제어 할 수있는 클라이언트 / 서버 시스템입니다.

이 존재 하며, 자세한건 사이트에서 직접 참조 바람
https://www.seleniumhq.org/projects/

Cross Browser Test와 UI Test에 주로 이용되는 프로젝트는 `Selenium-WebDriver`이며, 이는 [W3스펙에 의한 WebDriver](https://w3c.github.io/webdriver/) 구현체를 쉽게 사용하게 추상화하여 여러 WebDriver를 지원하고 프로그래밍이 가능한 Web Browser를 제공 함

Selenium-Webdriver는 다양한 언어를 지원하기 위해 라이브러리를 제공 합니다.
* Java
* C#
* Javascript(Node.js)
* Python

Spring 진영에서는 End-To-End Test 지원을 위한 Spring MVC Test중 Selenium을 사용 할 수 있습니다.
https://docs.spring.io/spring-test-htmlunit/docs/current/reference/html5/

```java
# Spring MVC Test 코드(HtmlUnit을 활용)
assertThat(newMessagePage.getUrl().toString()).endsWith("/messages/123");
String id = newMessagePage.getHtmlElementById("id").getTextContent();
assertThat(id).isEqualTo("123");
String summary = newMessagePage.getHtmlElementById("summary").getTextContent();
assertThat(summary).isEqualTo("Spring Rocks");
String text = newMessagePage.getHtmlElementById("text").getTextContent();
assertThat(text).isEqualTo("In case you didn't know, Spring Rocks!");
```

#### 지원하는 드라이버들 (2019.04.28 기준)
Browser | Maintainer | Versions Supported
--- | --- | ---
Chromium | [Chromium](https://sites.google.com/a/chromium.org/chromedriver/) | All versions
Firefox | [Mozilla](https://github.com/mozilla/geckodriver) | 54 and newer
Internet Explorer | Selenium | 6 and newer
Opera | Opera [Chromium](https://github.com/operasoftware/operachromiumdriver) / [Presto](https://github.com/operasoftware/operaprestodriver) | 10.5 and newer
Safari | [Apple](https://webkit.org/blog/6900/webdriver-support-in-safari-10/) | 10 and newer

selenium-webdriver를 공부하기 위한 예제
`크롬`과 `파이어폭스` 2가지 `webdriver를 받아 구글 검색 예제`를 작성
자세한 내용은 아래의 링크를 참조 바람.
https://seleniumhq.github.io/selenium/docs/api/javascript/index.html

### Puppeteer
![Puppeteer](https://user-images.githubusercontent.com/10379601/29446482-04f7036a-841f-11e7-9872-91d1fc2ea683.png)

Puppeteer는 Headless Chrome Node.js API입니다. Puppeteer는 Chrome과 통신하기 위해 `Chrome DevTools Protocol`를 사용 하며, Chrome Devtools의 모든 기능을 Node.js에서 사용 가능하게 해줍니다. 개인적으로 API 호출 하나하나가 Browser를 사용하듯이 직관적이라고 느꼇습니다.
[Puppeteer API DOC](https://pptr.dev)
[Chrome DevTools Protocol Viewer](https://chromedevtools.github.io/devtools-protocol/)
[Awesome Puppeteer(활용 예제 모음)](https://github.com/transitive-bullshit/awesome-puppeteer)

Puppeteer 특징은 다음과 같습니다.
* 페이지의 스크린 샷과 PDF를 생성
* SPA (Single Page Application)를 크롤링하고 사전 렌더링 된 내용 (즉, "SSR"(Server-Side Rendering))을 생성
* Form 테스트, UI 테스트, 키보드 입력 등 자동화
* 최신 JavaScript 및 브라우저 기능을 사용하여 테스트
* 사이트 의 타임 라인 추적 을 캡처하여 성능 문제를 진단
* Chrome 확장 프로그램을 테스트

#### Chrome DevTools Protocol
DevTools Protocol는 Chrome을 직접 사용 할 수있는 프로토콜이며 다양한 언어의 라이브러리가 존재 하지만, Puppereer처럼 구글에서 관리되는 프로젝트가 아닙니다.
다른 언어의 라이브러리는 [Awesome chrome-devtools](https://github.com/ChromeDevTools/awesome-chrome-devtools) 참조 하세요

### HTTP Client
리눅스에서 사용하는 curl, wget등이 있으며 각각 지원언어에 따른 HTTP Client들이 존재 합니다.
HTTP를 사용하기에 가장 간단한 방법이며 Web의 정보를 쉽게 취득 할 수 있습니다.

```bash
# 구글 뉴스 스크래핑
curl https://news.google.com/?tab=wn&hl=ko&gl=KR&ceid=KR:ko > google_news.html
```

## 비교
 
 종류 | 속도 | Community | API DOC(주관적임) | 지원 언어 | Javascript 실행
 --- | --- | --- | --- | --- | ---
 Selenium-Webdriver | 보통 | 활발 | 보통 | Java, C#. Python, Javascript(Node.js) | O 
 Puppeteer | 빠름 | 적음 | 좋음 | Javascript(Node.js) | O
 HTTP Client | 매우빠름 | 활발 | ? | 다양 | X
 
 ### 속도
 #### HTTP Client
 ```javascript
const https = require('https');

https.get('https://news.google.com/?tab=wn&hl=ko&gl=KR&ceid=KR:ko', res => {
    res.setEncoding('utf8');
})
```
 ![Node.js HTTP Client](https://user-images.githubusercontent.com/6037055/57009725-6e3af400-6c33-11e9-9dd7-ab6bd88d8a34.png)
 
 #### Selenium
 ```javascript
require('chromedriver')
const {Builder, By, Key, until} = require('selenium-webdriver')

(async (browserName) => {
    let driver = await new Builder().forBrowser('chrome').build()
    try {
        await driver.get('https://news.google.com/?tab=wn&hl=ko&gl=KR&ceid=KR:ko')
    } catch (e) {
        console.error(e)
    } finally {
        await driver.quit()
    }
})()
```
![Node.js Selenium-WebDriver](https://user-images.githubusercontent.com/6037055/57009850-1ea8f800-6c34-11e9-8495-f7cfa601ec9a.png)
 
 #### Puppeteer
 ```javascript
const puppeteer = require('puppeteer');

(async () => {
    const browser = await puppeteer.launch({headless: true});
    const page = await browser.newPage();
    await page.goto('https://news.google.com/?tab=wn&hl=ko&gl=KR&ceid=KR:ko')
    await browser.close();
})();
```
 ![Node.js Puppeteer(No Headless)](https://user-images.githubusercontent.com/6037055/57009934-b73f7800-6c34-11e9-8d7b-5a2368b48bc8.png)
 ![Node.js Puppeteer(Headless)](https://user-images.githubusercontent.com/6037055/57009979-fbcb1380-6c34-11e9-9cb3-73c450e66cbd.png)
 
 
 
 ## 정리 
 >* 단순 HTML Content만 Scraping 할 경우 HTTP Client 추천
 >* 크로스 브라우저 UI 테스트가 필요할 경우 와 다양한 지원을 원하면 Selenium 추천
 >* 보안과 Javascript 구동이 필요하고 Chrome의 기능을 구체적으로 사용 하고 싶을 경우 Pupperteer(DevTools Protocol) 추천

P.S 
[Puppeteer 프로젝트들(Awesome Puppeteer)](https://github.com/transitive-bullshit/awesome-puppeteer)을 살펴 보는 와중에 눈에 띄는 재미있는 프로젝트를 2개 발견 하였습니다.

* [puppeteer-cluster](https://github.com/thomasdondorf/puppeteer-cluster) - Worker를 생성해 CPU자원을 최대한 사용가능(모니터링 지원)
* [puppeteer-recorder](https://github.com/checkly/puppeteer-recorder) - 크롬 확장프로그램으로 녹화 기능을 통해 Puppeteer Node.js Script를 생성