---
title: 2018-해커톤 Crawler
url: 1752.html
id: 1752
categories:
  - 미분류
date: 2018-09-09 09:57:55
tags:
---

저희는 nurikabe 홈페이지에서 퍼즐의 크롤링을 담당했습니다   개발 순서는

1.  https://www.puzzle-nurikabe.com/ 사이트에 접속 한 다음
2.  퍼즐 부분의 html 태그를 가져 옵니다.
3.  html태그를 2차원 int로 변환 하여 던져 줍니다.

개발 과정 중에 사용한 Tool과 라이브러리는

*   JHipster로 프로젝트 생성
*   jBrowserdriver라는 Headless Browser를 통한 크롤링
*   Jackson Json Paser로 데이터 JSON컨버팅

을 하였습니다.