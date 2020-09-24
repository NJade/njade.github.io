---
title: JPA 영속성
author: Njade
date: 2020-09-25 01:00:00 +0900
categories: [JPA]
tags: [JPA]
---

이 게시글은 인프런의 [김영한님의 강의](https://www.inflearn.com/course/ORM-JPA-Basic)를 보고 정리한 것입니다.

---

## 영속성 컨텍스트란
---
- 엔티티를 영구 저장하는 환경
- 영속성 컨텍스트는 논리적인 개념
* 엔티티 생명주기
    * 비영속 (new/transient): 영속성 컨텍스트와 무관한 상태
    * 영속 (managed): 영속성 컨텍스트에 관리되는 상태
    * 준영속 (detached): 영속성 컨텍스트에 저장되었다가 분리된 상태
    * 삭제 (removed): 삭제된 상태

```java
Member member = new Member() // 비영속 상태
em.persist(member) // 영속 상태
em.detach(member) // 준영속 상태
em.remove(member) // 삭제 상태
```
---

## 영속성 컨텍스트의 장점
---
* 1차 캐시 - 트랜잭션에서만 캐싱하기 때문에 큰 이점은 없음.
* 동일성 보장 (object1 == object2 => true를 보장)
* 트랜잭션 쓰기 지연 (Transactional write-behind) - 쓰기 지연 SQL 저장소가 쌓았다가 sql을 보냄. property에서 jdbc batch를 사용 가능

```xml
<property name="hibernate.jdbc.batch_size" value="10"/>
```
* 변경 감지 (Dirty Checking) - 커밋 시 스냅샷과 엔티티를 비교해서 update query를 생성
* 지연 로딩 (Lazy Loading)
---

## flush
---
* 영속성 컨텍스트의 내용을 DB에 동기화
1. 변경감지
2. Query 생성
3. DB에 query 전송
* 자동 콜 시점
1. em.flush()
2. 트랜잭션 커밋
3. JPQL 쿼리 실행 시

## 준영속 상태
* 영속성 컨텍스트에서 분리되어 영속성 컨텍스트가 제공하는 기능 지원을 받지 못 함.
```java
em.detach(object) // 하나
em.clear() // 전체
em.close()
```
* 준영속 상태의 entity를 조회하면 select 쿼리가 다시 날라감.

# Reference
- [인프런](https://www.inflearn.com/course/ORM-JPA-Basic)
