---
title: 오픈 그래프 프로토콜(Open Graph protocol)
url: 97.html
id: 97
categories:
  - 미분류
date: 2017-01-22 14:31:15
tags:
---

오픈 그래프 프로토콜은 페이스북에서 정의된 메타 태그 프로토콜 이라고 한다. 페이스북,트위터에서 링크 삽입시 해당사이트의 메타태그를 긁어와서 사이트정보를 미리 보여주는 정도의 용도로 생각하면 될거 같습니다. **적용 예)**

<meta property="og:title" content="The Rock" />  <meta property="og:type" content="video.movie" />  <meta property="og:url" content="http://www.imdb.com/title/tt0117500/" />  <meta property="og:image" content="http://ia.media-imdb.com/images/rock.jpg" />

 

**오픈 그래프 프로토콜 예시)**
-------------------

예시 링크 : [https://www.youtube.com/watch?v=B2z5C_Mesx0](https://www.youtube.com/watch?v=B2z5C_Mesx0)   **트위터 예)** ![201604241514340](https://ahea.files.wordpress.com/2017/01/201604241514340.png) **페이스북 예)** ![201604241515580](https://ahea.files.wordpress.com/2017/01/201604241515580.png) **카카오톡 예)** ![kakaolink.png](https://ahea.files.wordpress.com/2017/01/kakaolink.png) 위의 정보를 어떻게 가져와서 보여주는지 알아 보겠습니다.

**오픈 그래프 프로토콜을 사용해 보자.**
------------------------

[http://ogp.me/](http://ogp.me/) 사이트를 방문 하면 [Implementations](http://ogp.me/#implementations) 메뉴에 디버깅 툴 및 플러그인 등 각종 링크가 있습니다. 여기서

*   [Facebook Object Debugger](http://developers.facebook.com/tools/debug/) \- Facebook's official parser and debugger

에 대해 알아보고 어떻게 적용할지 알아 보겠습니다. [Facebook Object Debugger](http://developers.facebook.com/tools/debug/) \- Facebook's official parser and debugger는 페이스북에서 제공하는 오픈 그래프 프로토콜이 어떤건지 알수 있는 디버깅 툴 입니다. 해당페이지에 접속하여서 url을 넣고 Debug버튼 페이스북의 링크 공유시 어떻게 나올지 나타내게 됩니다. [Facebook Object Debugger](http://developers.facebook.com/tools/debug/) 예) ![developers_facebook_com_20160424_153500.png](https://ahea.files.wordpress.com/2017/01/developers_facebook_com_20160424_153500.png) 간단한 정보만 필요함으로 직접 작성하였습니다.

      Document doc;  String url = "https://www.youtube.com/watch?v=B2z5C_Mesx0";  Map<String, List<String>\> result = new HashMap<String,List<String>>();    try {             doc = Jsoup.connect(url).get();             Elements ogElements = doc.select("meta\[property^=og\], meta\[name^=og\]");  for (Element e : ogElements) {  String target=target = e.hasAttr("property") ? "property":"name";    if(!result.containsKey(e.attr(target))){  result.put(e.attr(target), new ArrayList<String>());                 }                 result.get(e.attr(target)).add(e.attr("content"));             }    for(String s : result.keySet())  System.out.println(s + " : " \+ result.get(s));    } catch (Exception e){             e.printStackTrace();         }

**실행결과**
--------

og:image : \[https://i.ytimg.com/vi/B2z5C_Mesx0/hqdefault.jpg\]  og:type : \[video\]  og:site_name : \[YouTube\]  og:title : \[SNL코리아7 - 인스턴트 LOVE "3분 남친" by 에릭남, 정이랑 (2016.04.16)\]  og:video:type : \[text/html, application/x-shockwave-flash\]  og:video:height : \[720, 720\]  og:video:url : \[https://www.youtube.com/embed/B2z5C\_Mesx0, http://www.youtube.com/v/B2z5C\_Mesx0?version=3&autohide=1\]  og:url : \[https://www.youtube.com/watch?v=B2z5C_Mesx0\]  og:video:secure_url : \[https://www.youtube.com/embed/B2z5C\_Mesx0, https://www.youtube.com/v/B2z5C\_Mesx0?version=3&autohide=1\]  og:video:tag : \[SNL코리아7, 인스턴트 LOVE 3분 남친, 에릭남, 정이랑, 2016.04.16\]  og:description : \[SNL코리아7 - 인스턴트 LOVE "3분 남친" by 에릭남, 정이랑 (2016.04.16) #일부 국가 차단으로 다시 업로드 했습니다 ^^\]  og:video:width : \[1280, 1280\]

  추가로 자바스크립트 적용 소스도 올리겠습니다. 유튜브 비디오 처리랑 일반 웹사이트만 추가했습니다.

this.ajaxUrlConnet = function (data) {         $.ajax({  url: '/tbbswrite/opengraph',  method: "GET",  async: false,  cache: false,  contentType: true,  processData: true,  data: {'url': data}         }).done(function(c) {  $.each(c, function(key,value){  console.log(key + ' : ' \+ value);             })  var html='';  if('video'.indexOf(c\['og:type'\]\[0\]) >= 0){                 html+='<pre style="width: 300px;overflow: hidden;" onclick="location.href=\\''+c\['og:url'\]\[0\]+'\\'">';  if( !(c\['og:video:secure_url'\]\[0\] === undefined)){                     html+='+c\['og:video:secure_url'\]\[0\]+'">';                 }                 html+='<h1><b>'+c\['og:title'\]\[0\]+'</b></h1>';  if( !(c\['og:description'\] === undefined)){                     html+='<p>'+c\['og:description'\]+'</p>';                 }                 html+='<p><a href="'+c\['og:url'\]\[0\]+'">'+c\['og:url'\]\[0\]+'</a></p>';                 html+='</pre>';                 context.invoke('editor.pasteHTML', html);  } else {                 html+='<pre style="width: 300px;overflow: hidden;" onclick="location.href=\\''+c\['og:url'\]\[0\]+'\\'">';  if( !(c\['og:image'\]\[0\] === undefined)){                     html+='<img src="'+c\['og:image'\]\[0\]+'" style="width: 100%;height: 100%;" />';                 }                 html+='<p><h1><b>'+c\['og:title'\]\[0\]+'</b></h1></p>';  if( !(c\['og:description'\] === undefined)){                     html+='<p>'+c\['og:description'\]+'</p>';                 }                 html+='<p><a href="'+c\['og:url'\]\[0\]+'">'+c\['og:url'\]\[0\]+'</a></p>';                 html+='</pre>';                 context.invoke('editor.pasteHTML', html);             }         }).fail(function() {             alert('error = ' \+ data);         });  }

  해당 프로젝트는 github에 올려놓았습니다. 문제점이 있거나 잘못된 코딩습관이 있으면 코멘트 부탁드리겠습니다^^ github : [https://github.com/kji6252/JsoupOpenGrahpTest](https://github.com/kji6252/JsoupOpenGrahpTest)