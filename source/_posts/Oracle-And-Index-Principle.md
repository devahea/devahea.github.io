---
title: "Oracle And Index Principle"
date: 2019-04-22 09:57:29
tags: 
	- oracle
	- database
img: https://docs.oracle.com/cd/E11882_01/server.112/e40540/img/cncpt239.gif
---
# 개발자도 알아야 할 Oracle의 원리


오라클의 쿼리가 느린경우 어떻게 해야 할까요???

![execution plan](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQCTXyFwB278QdJCmLQIrbWYLPIZtMBa7txO4Txa2ao6P6YJUuh)

개발자는 실행계획을 통해 옵티마이저가 어떻게 동작하는지 확인합니다

상황에 따라 다르겠지만 Table Full Scan을 Index Scan으로 바꿔주면 훨신 빠른 결과를 만들어 낼수 있습니다

힌트를 사용하는 방법도 있겠죠

튜닝은 개발자에게 참 어려운 일입니다

대체 인덱스는 정확히 언제 걸어야 하고 힌트는 어떻게 걸어야 하는걸까요?

이번 포스팅에서는 오라클에서 테이블이 어떻게 구성되는지부터 인덱스와 힌트를 사용하면 내부적으로는 어떻게 진행되는지를 알아보려고 합니다

---

### 앞으로 다뤄질 내용
- 스키마와 테이블
	- 테이블의 구성을 이해하고 데이터가 저장되는 방법을 이해한다
	 
![table architecture](https://docs.oracle.com/cd/E11882_01/server.112/e40540/img/cncpt284.gif)

- FullScan과 IndexScan(인덱스의 종류와 동작원리)
	- 인덱스의 종류
	- B-tree
	- Hash

![btree](https://docs.oracle.com/cd/E11882_01/server.112/e40540/img/cncpt244.gif)

	
- 나쁜 힌트 만들기	
	- 힌트의 종류
	- 잘못사용하는 예시들

- 인덱스 없이도 할 수 있는 오라클 튜닝
	- 컬럼 순서만으로도 빨라지는 마법 
	- 최악의 정렬 	