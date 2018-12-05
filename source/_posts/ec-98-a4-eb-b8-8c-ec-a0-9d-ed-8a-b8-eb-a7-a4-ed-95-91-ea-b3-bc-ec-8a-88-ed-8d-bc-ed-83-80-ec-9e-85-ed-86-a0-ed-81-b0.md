---
title: 오브젝트 매핑과 슈퍼 타입 토큰
tags:
  - java
  - reflection
url: 471.html
id: 471
date: 2017-03-04 02:14:12
---

자바 프로젝트를 진행 하면서 외부 데이터를 가지고 ValueObject(이하 VO)에 매핑하고 여러가지 연산작업을들 합니다. 그중에서도 VO의 정보를 가져와 매핑하는 방법에 대해서 궁금하게 되었고 자바에서 제공하는 리플렉션API와 간단한 예제들을 통해서 오브젝트 매핑에 대해 알아 보겠습니다.

클래스 정보 가져오기
===========

![](https://ahea.files.wordpress.com/2017/03/471_uxqrwdbcro.png)

![](https://ahea.files.wordpress.com/2017/03/471_zlofw7r-8r.png)

![](https://ahea.files.wordpress.com/2017/03/471_tges7yfoa1.png)

자바는[ java.lang.reflect패키지의 API](http://docs.oracle.com/javase/8/docs/api/java/lang/reflect/package-summary.html)를 가지고 클래스의 정보와 필드 어노테이션 정보까지 모두 가져 올수 있는 리플렉션을 제공 합니다.

그래서 이렇게 구할수 있는 정보를 가지고 외부에서 가져오는 json 데이터나 sql문을 가져올시 매핑을 하여서 해당 오브젝트로 값을 삽입해 넘겨줄수 있습니다.

조금더 편리한 API
===========

리플렉션 API로도 훌륭하게 매핑이 가능하지만 조금더 사용하기 편리하고 안전하게 매핑할수 있도록 자바에서 제공하는 [java](http://docs.oracle.com/javase/8/docs/api/java/beans/package-summary.html)[.beans](http://docs.oracle.com/javase/8/docs/api/java/beans/package-summary.html) 에서 제공하는 API를 사용 하겠습니다.

![](https://ahea.files.wordpress.com/2017/03/471_tkq0crge72.png)

![](https://ahea.files.wordpress.com/2017/03/471_jbtifhc3is.png)

BeanInfo 객체를 생성할 땐 Introspector.getBeanInfo라는 static factory methode를 이용하여 빈정보를 생성한뒤 각각의 BeanDescriptor, PropertyDecriptors, MethodDescriptors를 가져와서 리플렉션보다는 편리하게 클래스 정보를 가져 올수 있습니다.

여기에서 map형태나 json형태의 값을 가져 왔을때 오브젝트 매핑을 해야 하는데 PropertyDecriptors의 출력결과를 자세히 보시면  각각의 property에 대해서 redeMethod와 writeMethod정보를 보여주고 있습니다. 이걸 활용하여 맵에 프로퍼티이름과 값을 넣어두고 간단하게 매핑하는 예제를 보여드리겠습니다.

심플 매핑
=====

![](https://ahea.files.wordpress.com/2017/03/471_kpagpsnkrh.png)![](https://ahea.files.wordpress.com/2017/03/471_3qslmuyj-b.png)

Map에 프로퍼티 이름과 동일하게 값을 셋팅해주고 List에 한개씩 담았습니다.

![](https://ahea.files.wordpress.com/2017/03/471_aopqydizio.png)

![](https://ahea.files.wordpress.com/2017/03/471_gsdpa-3-fl.png)

소스를 보시면 셋팅된 데이터로 forEach문을 돌리면서 Map을 한개씩 가져옵니다 그러면서 데이터를 셋팅될 오브젝트를 생성하고(User u = new User();) forEach문 안에 property를 한개씩 꺼내오면서 property의 이름을 으로  Map의 키값을 검색하여 Map의 Value를 가져와 propertyDescriptor.getWriteMethod를 이용하여 값을 셋팅 해줍니다. 보기엔 복잡해 보이지만 하나씩 따라가보면 비교적 쉽게 느껴 지실수 있습니다.

타입 토큰
=====

예제 소스 코드중에서 Introspector.getBeanInfo(User.class)가 있는데 보통 User.class를 타입 토큰 이라고 합니다. 그런데 자바에서는 아쉽게도 타입 토큰에는 컴파일시 제너릭정보를 담을수가 없어서 class정보만을 가져올수가 있습니다.(C#은 자바와는 다르게 제너릭정보를 바로 가져올 수 있다고 하네요...)

![](https://ahea.files.wordpress.com/2017/03/471_lgqlm85e8m.png)

그래서 이걸 해결하기 위해 나온것이 슈퍼 타입 토큰 이라고 합니다.

슈퍼 타입 토큰
========

슈퍼 타입 토큰은 클래스의 제너릭 정보까지 가져오는 방식 입니다. 익명클래스를 작성하여 상속하면 상속된 정보는 컴파일시 지워지지가 않는다고 하여 익명클래스 상속방식으로 슈퍼 타입 토큰을 가져옵니다.

![](https://ahea.files.wordpress.com/2017/03/471_vfbsbjeare.png)

![](https://ahea.files.wordpress.com/2017/03/471_1toza74z_l.png)

예제 소스를 보시면 extends가 된 클래스(슈퍼 클래스)는 가져오지만 List의 String정보는 못가져오고 있습니다. 이를 이용하여 슈퍼 타입 토큰을 이용하면 제너릭 정보를 가져올수가 있습니다.

위의 예제에서 슈퍼 타입 토큰을 사용하기 편하게 클래스를 정의해 보겠습니다.

타입 토큰과 슈퍼 타입 토큰 비교
==================

슈퍼 타입 토큰을 사용을 간소화 하기 위해서 클래스를 작성하였습니다.

![](https://ahea.files.wordpress.com/2017/03/471_dqeow7mq0n.png)

![](https://ahea.files.wordpress.com/2017/03/471_s0gvfh7ggk.png)

ParametertizedType은 파라미터형의 타입을 표현하는 인터페이스 입니다.

![](https://ahea.files.wordpress.com/2017/03/471_aqpw2yeejk.png)

![](https://ahea.files.wordpress.com/2017/03/471_cjexy53exq.png)

슈퍼 타입 토큰의 경우 제너릭정보를 모두 불러오지만 타입 토큰은 제너릭정보를 담을수가 없어 제한적일수바께 없습니다.

예제1\. JDBC 예제
=============

GitHub : [https://github.com/kji6252/ORM-example2.git](https://github.com/kji6252/ORM-example2.git)

앞에서 알아본 클래스정보를 가져올수있는 BeanInfo와 슈퍼타입 토큰을 활용한 JDBC 오브젝트 매핑 예제를 작성 했습니다.

![](https://ahea.files.wordpress.com/2017/03/471_-qum1ndn3j.png)

그림. JDBC 예제 구성도

![](https://ahea.files.wordpress.com/2017/03/471_hmho5-em2j.png)

그림. JDBC 예제 메인

![](https://ahea.files.wordpress.com/2017/03/471_eyetr4td8y.png)

OMMaper에서는 query메서드 에서 각각 원하는 결과를 리턴 할 수 있는 classQuery,listQuery,mapQuery 로 분기 한다음 ResultSet과 BeanInfo의 PropertyDescriptor로 매핑하여 최종 결과를 리턴 하게 됩니다.

![](https://ahea.files.wordpress.com/2017/03/471_zktv1jz3sg.png)

예제2\. Spring JDBC 예제
====================

![](https://ahea.files.wordpress.com/2017/03/471_dfcoldcfv1.png)

스프링에서 제공하는 SqlQuery를 상속받으면 protected abstract RowMapper<T\> newRowMapper(Object\[\] parameters, Map<?, ?\> context); 메서드를 구현해야 합니다.

스프링 JDBC예제에서도 예제1과 동일한 방식으로 매핑을 하였습니다.

![](https://ahea.files.wordpress.com/2017/03/471_ilowdvj_uu.png)

예제 1과 동일하게 구성 하였습니다.

![](https://ahea.files.wordpress.com/2017/03/471_cptgockuxu.png)

SqlQuery에서 아이디값으로 sql문을 실행하여 RowMapper로 매핑하여 결과를 가져 옵니다.

![](https://ahea.files.wordpress.com/2017/03/471_r3amagw3pb.png)