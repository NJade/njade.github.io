---
title: JPA Proxy and Relation
author: Njade
date: 2020-12-17 01:30:00 +0900
categories: [JPA]
tags: [JPA]
---

이 게시글은 인프런의 [김영한님의 강의](https://www.inflearn.com/course/ORM-JPA-Basic)를 보고 정리한 것입니다.
실전 예제의 팁 등을 제외한 코드는 첨부하지 않습니다. 강의를 봐주세요.

---

## 프록시
---
* 연관관계에 있는 객체를 함꼐 가져올 것인지 나중에 가져올 것인지 정해야 할 필요가 있다.
* 이런 로직을 위해 JPA에서 em.find()가 아닌 em.getReference()를 제공한다.
* em.getReference()는 DB조회를 미루는 프록시 엔티티 객체를 조회한다.
* 프록시는 실제 클래스를 상속 받아서 만들어짐.
* 프록시는 실제 클래스인 target을 가지고 있음.
* 실제 클래스와 겉 모양이 동일.
* 사용하는 입장에서는 이론상 실제 객체와 프록시를 구분하지 않고 사용할 수 있다.
* 사용자가 프록시 객체에 데이터의 값을 조회하면 프록시 객체는 영속성 컨텍스트에게 실제 데이터(초기화)를 요청하게 된다.
* 이후에는 이미 값을 가지고 있기 때문에 영속성 컨텍스트에 요청하지 않는다.

---

## 프록시 특징
---
* 프록시 객체는 처음 사용할 때만 초기화
* 프록시 객체가 실제 엔티티로 바뀌지 않고 실제 엔티티에 접근이 가능해지는 것이다.
* 프록시 객체는 원본 엔티티를 상속받기 때문에 ==으로 타입 체크시 실패할 수도 있기 때문에 instance of를 사용하여야 함.
```java
Entity e1;
Entity e2;
e1.getClass() == e2.getClass() // 결과를 확신할 수 없음.
// 아래와 같이 사용
e1 instanceof Entity 
e2 instanceof Entity
```
* 영속성 컨텍스트에 찾는 엔티티가 이미 있으면 getReference()를 호출해도 실제 Entity가 반환된다.
* JPA에서는 같은 영속성 컨텍스트의 같은 키의 객체를 == 비교하면 무조건 true가 나와야 한다.
```java
Entity e1 = em.find();
Entity e2 = em.getReference();
System.out.println(e2.getClass()) // Entity
System.out.println(e1 == e2); // true
// ===========================
Entity e1 = em.getReference();
Entity e2 = em.find();
System.out.println(e2.getClass()) // Proxy
System.out.println(e1 == e2); // true
```
* 영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일 때 프록시를 초기화하면 LazyInitializationException을 던진다.
* 트랜잭션과 영속성 컨텍스트의 주기를 맞추기 때문에 자주 보게 됨.

---

## 프록시 확인
---
* PersistenceUnitUtil.isLoaded(Object entity)를 통해 프록시 인스턴스 초기화 여부를 확인할 수 있다.
* entity.getClass().getName()을 통해 프록시 클래스를 출력해 볼 수 있다.
* org.hibernate.Hibernate.initialize(entity)를 통해 프록시를 강제 초기화할 수 있다.
* JPA에 실제로는 강제 초기화가 없다.

---

## 즉시 로딩과 지연 로딩
---
* 연관 관계 객체를 거의 사용하지 않는다면 연관 관계 객체를 조회 때 함께 조회하게 되면 성능의 손해가 생긴다.
* @ManyToOne 등의 어노테이션에 fetch 속성에 FetchType.LAZY를 사용하면 연관 객체를 프록시 객체로 받아올 수 있다.
* 연관 관계 객체를 거의 항상 사용한다면 FetchType.EAGER를 통해 한 번에 조회하는 것이 좋다.
* 이 경우 프록시 객체가 아닌 진짜 Entity 객체가 반환된다.
* 실무에서는 가급적 지연 로딩만 사용
* 즉시 로딩 시 예상치 못한 SQL이 발생할 수 있다.
* 테이블의 연관관계가 복잡한 경우 굉장히 많은 조인 쿼리가 발생할 수 있다.
* 또 JPQL에서 N+1 문제를 일으킨다.
* 어떤 Entity의 모든 데이터를 가져오는 경우, 쿼리 1번으로 모든 Entity를 가져온 후 각 Entity의 연관 객체들을 위한 SQL이 N번 나간다.
* @ManyToOne, @OneToOne은 기본이 즉시 로딩이다.
* @OneToMany, @ManyToMany는 기본이 지연 로딩이다.
* 모든 경우에 지연 로딩을 사용해야 한다.
* JPQL fetch 조인이나 엔티티 그래프 기능을 사용해야 한다.

---

## 영속성 전이 CASCADE
---
* 특정 엔티티틀 영속 상태로 만들 때 연관 엔티티도 함께 연속 상태로 만들고 싶을 때 사용
* cascade를 주지 않으면 모든 엔티티를 persist시켜줘야 한다.
* @OneToMay와 같은 어노테이션에 cascade=CascadeType.ALL을 추가하면 연관 엔티티를 명시적으로 persist 해주지 않아도 된다.
* 영속성 전이는 연관관계를 맵핑하는 것과 전혀 관련이 없고 연관 엔티티도 함께 영속화하는 편리함을 제공할 뿐이다.
* ALL, PERSIST, REMOVE 등의 옵션이 있다.
* 단일 엔티티에 완전히 종속적이어서 라이프사이클이 거의 유사한 경우에 유용하고 다른 경우에는 문제를 일으킬 가능성이 높아진다.

---

## 고아 객체
---
* 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동 삭제
* orphanRemoval = true 라는 옵션이 존재
* 참조하는 곳이 하나일 때, 특정 엔티티가 개인 소유할 때 사용해야 한다.
* @OneToOne, @OneToMany만 사용이 가능
* 개념적으로 부모 엔티티가 제거되면 자식은 고아가 되기 때문에 이 기능을 활성화하면 부모 엔티티를 제거하면 자식 엔티티도 제거된다. (CascadeType.REMOVE와 유사)

---

## 생명주기
---
* 영속성 전이와 고아 객체옵션을 모두 활성화하면 부모 엔티티를 통해 자식 엔티티의 생명주기를 제어할 수 있다.
* 도메인 주도 개발(DDD)의 Aggregate Root 개념을 구현할 때 유용하다.

---

# Reference
---
- [인프런](https://www.inflearn.com/course/ORM-JPA-Basic)
