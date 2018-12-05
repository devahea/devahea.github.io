---
title: '[2017해커톤]Docker 를 활용한 MariaDB 컨테이너 띄우기'
url: 1444.html
id: 1444
categories:
  - 미분류
date: 2017-08-15 10:18:56
tags:
---

![](https://ahea.files.wordpress.com/2017/08/1444_iwkb1js2dj.png)

1.  Kitematic 에서 MariaDB을 찾아 Create버튼을 클릭 합니다.

![](https://ahea.files.wordpress.com/2017/08/1444_krgq1cjm4o.png)

2.  자동으로 Image를 다운받아 컨테이너까지 띄워지고 원하는 호스트 포트를 지정해 주시고

![](https://ahea.files.wordpress.com/2017/08/1444_tfoloaupma.png)

3.  환경 변수에는 root 비밀번호를 설정 합니다.(MYSQL\_ROOT\_PASSWORD)

![](https://ahea.files.wordpress.com/2017/08/1444_aevjvafbqe.png)

4.  호스트의 볼륨과의 연결을 통해 DB에 쌓이는 데이터파일을 호스트PC의 지정 디렉토리에 저장 할수 있게 설정할수 있습니다.