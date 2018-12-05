---
title: '[2017해커톤] Spring cross domain 이슈 처리'
url: 1465.html
id: 1465
categories:
  - 미분류
date: 2017-08-15 10:26:43
tags:
---

\[해커톤\] Spring Boot / Cross Domain 이슈
=====================================

해결법 1\. @CrossOrigin \[code lang=text\] @RequestMapping(value = Constant.URI\_CATEGORY\_TYPE_API) @CrossOrigin public String getCategoryType(){ List<Enum> categoryType = new ArrayList<>(); categoryType.add(CategoryType.Date); categoryType.add(CategoryType.Random); categoryType.add(CategoryType.Repo); return categoryType.toString(); } \[/code\] 해결법 2. JavaConfig \[code lang=text\] @Configuration public class WebConfiguration { @Bean public WebMvcConfigurer corsConfigurer() { return new WebMvcConfigurerAdapter() { @Override public void addCorsMappings(CorsRegistry registry) { registry.addMapping("/**"); } }; } } \[/code\] 참고 https://spring.io/guides/gs/rest-service-cors/ https://spring.io/blog/2015/06/08/cors-support-in-spring-framework