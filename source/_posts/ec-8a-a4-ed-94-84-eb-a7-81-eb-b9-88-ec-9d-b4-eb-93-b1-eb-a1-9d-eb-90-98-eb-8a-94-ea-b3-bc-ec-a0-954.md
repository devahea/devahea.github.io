---
title: 스프링 빈이 등록되는 과정(4)
url: 1227.html
id: 1227
categories:
  - 미분류
date: 2017-06-22 17:49:18
tags:
---

우리는 지난 포스팅을 통해 xml, java-config 방식의 빈등록 과정을 살펴봤으며 이번시간에는 내용을 정리해보려고 합니다. 예제들은 모두 Spring Boot을 기준으로 진행되었으나 핵심적인 원리는 스프링 모두 같습니다. 스프링이 올라간다는 것은 스프링의 컨텍스트가 올라간다는 건데 스프링 컨텍스트가 올라갈때 factory를 가지게 됩니다. (정확히는 Spring의 `ApplicationContext`는 `BeanFactory`를 구현한 구현체입니다, 물론 lazy, pre-loading의 차이점은 있습니다) 스프링은 빈설정에 따라 다르게 진행되는데 java-config인 경우 `AnnotationConfigApplicationContext`를 통해 동작을 확인해봤습니다. `AnnotatedBeanDefinitionReader`를 통해 componentScan을 하여 `BeanDefinition`을 정의하는 것을 확인했습니다. xml도 마찬가지로 `BeanDefinition`을 정의하는데요. `XmlBeanDefinitionReader`을 통해 xml파일의 document를 만들어서 진행하는것을 살펴봤습니다 xml, groovy의 경우 `ConfigurationClassBeanDefinitionReader`를 통해 읽어오는데 `ApplicationContext`에서 `PostProcessorRegistrationDelegate`에게 빈등록을 위임하면 딜리게이트가 빈등록을 진행하면서 `ConfigurationClassBeanDefinitionReader`까지 연결됬었죠 결국 어떤 식으로 설정을 하든 빈등록을 하려면 BeanDefinition으로 정의된 메타데이터를 만들면 되는것입니다. ![1](https://raw.githubusercontent.com/devload/devload.github.io/master/assert/image/fact_spring_bean4/1.png)