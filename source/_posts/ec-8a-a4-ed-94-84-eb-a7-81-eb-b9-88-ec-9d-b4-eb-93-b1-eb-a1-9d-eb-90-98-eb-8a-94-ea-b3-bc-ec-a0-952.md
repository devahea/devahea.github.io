---
title: 스프링 빈이 등록되는 과정(2)
url: 1219.html
id: 1219
categories:
  - 미분류
date: 2017-06-22 16:08:54
tags:
---

스프링 빈이 등록되는 과정(2)
=================

1편에서는 Spring Boot을 기반으로 스프링이 빈을 어떻게 등록하는지 알아봤습니다 아주 기본적으로는 `AnnotationConfigApplicationContext`에 대해 알아봤는데요 그 안에 있는 `AnnotatedBeanDefinitionReader`를 통해 진행하며 그 안에 `doScan`메소드를 살펴보며 빈을 등록하는 과정을 살펴봤습니다 이번에는 xml기반인 경우 어떻게 진행이 되는지 알아보겠습니다

XmlBeanDefinitionReader
-----------------------

spring은 xml기반의 빈등록을 하기 위해 `XmlBeanDefinitionReader`라는 녀석을 통해 진행하게 됩니다. `XmlBeanDefinitionReader`는 `AbstractBeanDefinitionReader`를 상속받고 있는데요. 어노테이션 기반에 사용되었던 `AnnotatedBeanDefinitionReader`도 이것을 상속받나? 했지만 `AbstractBeanDefinitionReader`는 상속받는 클래스가 없습니다. 왜일까요?? 어노테이션으로 등록하려는 빈들은 이미 클래스로더에서 꺼내올수 있기 때문이 아닐까 추측하지만 정답은 모르겠습니다 `AbstractBeanDefinitionReader`를 상속받는 클래스는 `GroovyBeanDefinitionReader`, `PropertiesBeanDefinitionReader`, `XmlBeanDefinitionReader`가 있습니다. 모두 리소스를 접근해야 하는 부분인데 이런 경우에는 `AbstractBeanDefinitionReader`를 상속받나 봅니다. 나중에 기회가 되면 RMI를 이용한 빈등록을 만들어보거나 위에 타입과 다른 경우의 빈등록 형식을 만들어보는것도 좋을거 같네요. 우선 Spring이 `XmlBeanDefinitionReader`에게 bean을 읽어오기 위한 과정부터 얘기해보려고 합니다 스프링은 컨텍스트가 생성되면서 `refresh`를 명령합니다. 그러면 이때 `ApplicatioContext`는 설정들을 다시 뒤지기 시작합니다. 그러면 최하위 `ApplicationContext`부터 자신이 해야할 `refresh`동작을 처리 한뒤 상위 `refresh`를 호출하게 되죠, 그러면 언젠가 `AbstractApplicationContext`에까지 `refresh`명령이 오게 됩니다. 이때 `AbstractApplicationContext`는 `invokeBeanFactoryPostProcessors`메소드를 호출하게 되는데 `invokeBeanFactoryPostProcessors`에서는 `PostProcessorRegistrationDelegate`라는 딜리게이트에게 빈등록을 위임하게 됩니다. 그러면 이 딜리게이트는 `BeanFactoryPostProcessor`리스트를 만들게됩니다. `BeanFactoryPostProcessor`라는 것은 `BeanPostProcessor`와 같이 빈 등록에 관여하는 녀석인데 다음에 아주 자세하게 살펴보도록 하겠습니다. 어쨋든 `BeanPostProcessor`에 `BeanDefinitionRegistry`를 넘겨주면서 `BeanFactoryPostProcessor`의 `postProcessBeanDefinitionRegistry()`메소드로 넘어가게 됩니다. Spring Boot의 경우 `BeanPostProcessor`는 `ConfigurationClassPostProcessor`로 되어 있는데요. 여기서 호출된 `postProcessBeanDefinitionRegistry`메소드에서는 파라미터로 넘어온 `BeanDefinitionRegistry`정보를 `BeanDefinitionHolder`로 바꾸는 작업을 먼저 진행하고 그후 `ConfigurationClassBeanDefinitionReader`를 통해 `BeanDefiniton`을 읽도록 처리를 진행하게 됩니다. `ConfigurationClassBeanDefinitionReader`에서는 `BeanDefinitionReader`를 고르는 작업을 진행하는데 `groovy` 또는 `xml`만 받게 되는거 같습니다. xml의 설정의 경우 `XmlBeanDefinitionReader`가 생성되어 결국 `XmlBeanDefinitionReader`의 `loadBeanDefinitions`가 호출되게 됩니다. ![1](https://raw.githubusercontent.com/devload/devload.github.io/master/assert/image/fact_spring_bean2/1.png) `SpringApplication`에서 `XmlBeanDefinitionReader`로 오는 호출 과정을 보면 다음과 같습니다 ![2](https://raw.githubusercontent.com/devload/devload.github.io/master/assert/image/fact_spring_bean2/2.png) `XmlBeanDefinitionReader`에서는 `loadBeanDefinitions`메소드가 호출됨으로 시작합니다 `Resource`타입으로 받은 파라미터를 기준으로 `doLoadBeanDefinitions`가 호출될때까지 `Resource`를 가공합니다 `doLoadBeanDefinitions`에서는 xml을 파싱하기 위해 Document로 만들어 registerBeanDefinitions로 넘기게 되며 BeanDefinitionDocumentReader를 통해 빈등록을 진행하게 됩니다