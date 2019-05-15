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
author: ������
---

## WebDriver
![WebDriver Flow](https://user-images.githubusercontent.com/6037055/57774314-29e04580-7755-11e9-8b1b-8f7be681c8ec.png)
`Web Browser �ڵ�ȭ`�� ���� �¾� ������, ���� `Cross Browser Test`�� `UI Test`�� Ȱ�� �մϴ�. WebDriver�� [W3���� ������ WebDriver ����](https://w3c.github.io/webdriver/)�� ���� �� ������ ���� ������ WebDriver�� ������ Selenium WebDriver�� ���� ���������� �ٷ� �� �ֽ��ϴ�. �Ʒ����� �� �������� ���� ����̹� �Դϴ�.

### �����ϴ� ����̹��� (2019.04.28 ����)
Browser | Maintainer | Versions Supported
--- | --- | ---
Chromium | [Chromium](https://sites.google.com/a/chromium.org/chromedriver/) | All versions
Firefox | [Mozilla](https://github.com/mozilla/geckodriver) | 54 and newer
Internet Explorer | Selenium | 6 and newer
Opera | Opera [Chromium](https://github.com/operasoftware/operachromiumdriver) / [Presto](https://github.com/operasoftware/operaprestodriver) | 10.5 and newer
Safari | [Apple](https://webkit.org/blog/6900/webdriver-support-in-safari-10/) | 10 and newer

## DevTools Protocol(Debugging Protocol)
![Edge DevTools Protocol](https://46c4ts1tskv22sdav81j9c69-wpengine.netdna-ssl.com/wp-content/uploads/mswbprod/sites/33/2018/05/b46a7b838118991ffd34a9092310ccf0-1024x501.png)
Web�� �����Կ� ���� Web ���� ����� ���� ��ȭ �Ǿ�� �ֽ��ϴ�. �̸� ��Ȱ�� �����ϱ� ���� �� ������������ ���������� ������� �� �� �ִ� `DevTools Protocol`�� ���� �մϴ�. 
![Chrome DevTools ���� ���](https://user-images.githubusercontent.com/6037055/57776332-24d1c500-775a-11e9-9437-54a537e0961a.png)
`DevTools Protocol`�� Web Application�� ������ �� �ֵ��� Elements, Network, Perfomance, TimeLime, Trace�� �پ��� ����� �����ϰ� �ֽ��ϴ�.

### �������κ�����
![DevTools Protocol API ���� ��Ȳ](https://user-images.githubusercontent.com/6037055/57775898-159e4780-7759-11e9-992a-652672b50d97.png)
[�������� ���� API](http://compatibility.remotedebug.org/)�� ���� ��� ������ API�� Ȯ�� �Ͻ� �� �ֽ��ϴ�.
�Ʒ��� DevTools Protocol ���� ������ �Դϴ�.
* [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/)
* [WebKit / Safari](https://github.com/WebKit/webkit/tree/master/Source/JavaScriptCore/inspector/protocol)
* [Node.js](https://chromedevtools.github.io/devtools-protocol/v8/)
* Firefox - [in development](https://groups.google.com/forum/#!msg/mozilla.dev.platform/4-4A8W-nP5g/Y9C9UkWTAAAJ)
* [Edge DevTools Protocol](https://docs.microsoft.com/en-us/microsoft-edge/devtools-protocol/)

## ��ó
* https://blogs.windows.com/msedgedev/2018/05/11/introducing-edge-devtools-protocol/
* https://seleniumhq.github.io/docs/wd.html
* https://stackoverflow.com/questions/50939116/what-is-the-difference-between-webdriver-and-devtool-protocol
* https://github.com/WICG/devtools-protocol