---
title: '[2017해커톤] Dom4J를 이용하여 Xml Export'
url: 1486.html
id: 1486
categories:
  - 미분류
date: 2017-08-15 10:30:57
tags:
---

\[해커톤\]Dom4J를 이용하여 XML Export개발

     @Override
     public String export(Map<String, Object> data, Map option) {

         String rootElement = (String) option.get("rootElement");
         String recordElement = (String) option.get("recordElement");

         Document document = DocumentHelper.createDocument();
         Element root = document.addElement( rootElement );

         Element element = root.addElement( recordElement );

         data.keySet().forEach(s ->
 element.addElement(s).addText((String) data.get(s)));

         return document.asXML();
     }

@Override

     public String export(List<Map<String, Object>> datas, Map
 option) {

         String rootElement = (String) option.get("rootElement");
         String recordElement = (String) option.get("recordElement");

         Document document = DocumentHelper.createDocument();
         Element root = document.addElement( rootElement );

         datas.forEach(data -> {
             Element element = root.addElement( recordElement );
             data.keySet().stream().forEach(s ->

 element.addElement(s).addText(data.get(s).toString()));
         } );

         return document.asXML();

} Map을 xml로 변환하기 위해 Dom4J라이브러리를 사용하였습니다 1. DocumentHelper.createDocument()를이용하여Document객체를생성합니다. 2. 생성된 Document 에 addElement( string ) 를 통해 가장 상위 태그를 추가하게 됩 니다. 이떄 넘기는 String 파라미터가 태그명이 됩니다 3. Map의 데이터를 xml로 옮기기 위해서는 map을 for-each를 돌려야 하는데 맵은 keyset 을 통해 key 와 value 를 loop 를 돌릴수 있었습니다. row하나를 추가 하기 위 해 rootElement 에 addElement 를 하여 item을 추가하고 추가된 element 에 for- each 를 돌려서 나오는 맵 데이터를 하나씩 추가합니다 4. document에asXML()메소드를통해String으로리턴합니다