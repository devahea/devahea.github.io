---
title: Headless Browser란?
date: 2019-04-13 20:46:29
categories:
  - WEB
tags: 
  - HeadlessBrowser
  - Chrome
  - headless
  - javascript
---

## Headless Browser란?

위키피디아에서 발췌 했습니다.
> A headless browser is a web browser without a graphical user interface.

말 그대로 GUI환경이 아닌 브라우저를 뜻 합니다.

`CLI(Command Line Interface)`에서 동작하는걸 뜻하며, 프로그래밍하기에 용이 해집니다.

2000년대 까지만 하더라도 Javascript는 Web Page에서 동적으로 보이기 위해 사용 되어졌지만 Web의 성장함으로써 Frontend진영에서 Javascript Framework가 생기고, 이를 활용하여 개발한 SPA(Single Page Application)이 활발하게 되었습니다.

![react-angular-vue](https://user-images.githubusercontent.com/6037055/56088632-a0bcc100-5ebf-11e9-9ed2-db36b58c6a35.png)

SPA이전엔 HTTP Client(Jsoup, OKHttp)를 활용하여 Web Scraping(또는 Crawling)가능 해졌지만 Javascript를 실행해야지만 Scraping이 가능 해졌습니다.

Headless Browser의 등장으로 복잡한 인증 및 Javascript 실행으로 인해 실제 사람이 접속 하듯이 Scraping 할 수 있게 되었습니다.

## Http Client와 Headless Browser
HTTP Client는 URL입력과 동시에 Html Content를 바로 불러와 속도가 빠릅니다. 그에 비해 `Headless Browser는 백그라운드상에서 실제 Browser가 돌기에 상대적으로 속도가 느립`니다. 그래도 `SPA접근`과 `복잡한 인증`을 `쉽게 해결` 가능 한데요. 
Javascript 구동 여부는 Airbnb 페이지에서 확인 해보겠습니다.

CLI환경에서 에서 둘의 차이점을 확인 할 수 있습니다.
```bash
# curl HttpClient로 에어비앤비 Scraping
curl https://www.airbnb.co.kr > curl-Airbnb.html
```

```bash
# Chrome 헤드리스 모드로 에어비앤비 Scraping
chrome --headless --dump-dom --virtual-time-budget=3000 https://www.airbnb.co.kr > headless-Airbnb.html
```

```bash
ls -lh
-rw-r--r--    1 a1004024  staff   241K Apr 13 21:50 httpclient-Airbnb.html
-rw-r--r--    1 a1004024  staff   327K Apr 13 21:50 headless-Airbnb.html
```
두개 파일의 용량을 보면 `HTTP Client 결과는 241K`이며 `Headless Browser의 결과는 327K` 였습니다.

두개의 파일을 비교 해도 가져올수 있는 데이터를 확인 할 수있었습니다.

Client | 속도 | 인증처리 | Jascript실행여부 | 종류
--- | --- | --- | --- | ---
Http Client | 빠름 | 어려움 | X | curl, wget, 그외 라이브러리들
Headless Browser | 느림 | 쉬움 | O | Puppeteer, Selenium, PhantomJS 등

> Http Client를 사용가능한곳은 Http Client가 더 나은 선택 일 수 있습니다.

## Headless Browser를 통해 인증과 Scraping(네이버 카페)
개인적으로 좋아하는 Node.js 기반 Headless Browser인 [Puppeteer](https://pptr.dev)를 활용한 예제를 구성 하였습니다. 

실제 네이버 카페를 개설 한 후 비공개 카페의 글을 Scraping 하는 예제 입니다.

이 에제는 총 5단계의 거쳐 스크래핑을 할 예정 입니다.

1. Headless Browser 실행
2. 네이버 로그인
3. 네이버 카페 게시판 접근
4. 게시판의 게시글들의 URL 추출
5. 추출된 게시글의 내용 추출

샘플에 사용된 네이버 카페 게시판
![스크린샷 2019-04-14 오후 4 42 23](https://user-images.githubusercontent.com/6037055/56089853-63166300-5ed4-11e9-80c6-3ec21da2e842.png)

Scraping 해올 게시글
![noname](https://user-images.githubusercontent.com/6037055/56089871-95c05b80-5ed4-11e9-973b-0b5f4388b90f.png)

### 1. Headless Browser 실행

npm에서 `puppeteer` 패키지를 다운 받습니다.
```bash
npm i puppeteer
```

다운 받은 패키지를 Node.js 애플리케이션 개발에 불러옵니다.
```javascript
// src/naver-cafe.js 파일에 작성
const puppeteer = require('puppeteer');

(async () => {
    const browser = await puppeteer.launch({headless: false})
    const page = await browser.newPage()
    
    // 2, 3, 4, 5 내용 채울 예정
    
    await browser.close()
})();
```

### 2. 네이버 로그인
jQuery를 사용할때 쓰던 `CSS Selector`를 통해 `Element에 접근`을 하여 원하는 작업을 진행 합니다.
```javascript
    // 네이버 로그인 페이지 접근
    await page.goto('https://nid.naver.com/nidlogin.login?svctype=262144&url=http://m.naver.com/aside/')

    // 네이버 로그인
    await page.type('#id', '네이버 아이디', {delay: 100})
    await page.type('#pw', '네이버 비밀번호', {delay: 100})
    await page.click('[type="submit"]')
    await page.waitForNavigation()
```

### 3. 네이버 카페 게시판 접근
Scraping을 하고 싶은 네이버 게시판의 URL로 이동 합니다.
```javascript
    await page.goto('https://m.cafe.naver.com/ArticleList.nhn?search.clubid=29734529&search.menuid=1&search.boardtype=L')
```
 
### 4. 게시판의 게시글들의 URL 추출
`Chrome devtools`를 이용하여 Scraping 대상을 분석 하고 코드로 작성 하여야 합니다.
`Puppeteer`에서 제공하는 `Page`클래스의 `evaluate`함수를 이용하면 `Chrome devtools console`에서 작업하는거와 동일하게 작업이 가능 해집니다.
```javascript
    // 자유게시판 글 Scraping
    const articles = await page.evaluate(args => {
        const articles = []
        document.querySelectorAll('#articleListArea > ul li')
            .forEach((v, i) => {
                const article = {}
                article.title = v.querySelector('.tit').innerText
                article.nick = v.querySelector('.nick').innerText
                article.href = v.querySelector('a._articleListItem').href
                articles.push(article)
            })
        return articles
    })
    
    // 자유게시판 글들 확인
    console.log(articles)
```

위의 작업을 실행한 결과 입니다.
```javascript
[ { title: '게시글2 입니다.',
    nick: '페가수스',
    href:
     'https://m.cafe.naver.com/ArticleRead.nhn?clubid=29734529&articleid=3&page=1&boardtype=L&menuid=1' },
  { title: '게시글 1 입니다.',
    nick: '페가수스',
    href:
     'https://m.cafe.naver.com/ArticleRead.nhn?clubid=29734529&articleid=2&page=1&boardtype=L&menuid=1' } ]
```

### 5. 추출된 게시글의 내용 추출
추출된 각 게시글의 URL에 방문하여 게시글의 내용을 Scraping 합니다.

```javascript
    const articlePage = await browser.newPage()
    for (let article of articles) {
        await articlePage.goto(article.href)
        const content = await articlePage.$eval('#postContent', element => element.innerText)
        article.content = content;
    }

    // 자유게시판 글들을 내용까지 채움
    console.log(articles)
```

브라우저의 새탭을 열어 아까 가져온 게시글 URL에 차례대로 방문하여 내용을 Scraping 해옵니다.

```javascript
[ { title: '게시글2 입니다.',
    nick: '페가수스',
    href:
     'https://m.cafe.naver.com/ArticleRead.nhn?clubid=29734529&articleid=3&page=1&boardtype=L&menuid=1',
    content: '게시글2 입니다.\n\nABCDEFGHIJKLMNOPQRSTUVWXYZ' },
  { title: '게시글 1 입니다.',
    nick: '페가수스',
    href:
     'https://m.cafe.naver.com/ArticleRead.nhn?clubid=29734529&articleid=2&page=1&boardtype=L&menuid=1',
    content: '게시글1의 내용입니다. \n\n가나다라마바사아자차카타파하' } ]
```

## 마무리
Headless Browser란 Http Client에 비해 Javascript를 구동하여 추가 데이터를 가져 온다고 이해 하시면 좋습니다.

Node.js기반 Headless Browser인 Puppeteer를 사용한 네이버 카페 Scrping하였고, 이를 활용하면 네이버 로그인 인증도 쉽게 할 수 있고, 게시글을 사람이 직접 가져오는 방법처럼 예제를 구성 하였습니다.

소개한 예제는 Github에 공유 하겠습니다. 
다음 순서엔 다른 Headless Browser와의 비교를 소개 하겠습니다.
[https://github.com/kji6252/study-puppeteer](https://github.com/kji6252/study-puppeteer)
[https://pptr.dev](https://pptr.dev)