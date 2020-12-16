---
title: JPA Relation 3
author: Njade
date: 2020-12-16 22:50:00 +0900
categories: [JPA]
tags: [JPA]
---

이 게시글은 인프런의 [김영한님의 강의](https://www.inflearn.com/course/ORM-JPA-Basic)를 보고 정리한 것입니다.
실전 예제의 팁 등을 제외한 코드는 첨부하지 않습니다. 강의를 봐주세요.

---

## 상속관계 맵핑
---
* RDB는 상속 관계가 없음.
* 슈퍼타입 서브타입 관계가 객체 상속과 유사하여 객체의 상속관계를 슈퍼타입 서브타입으로 맵핑
* 슈퍼타입 서브타입 DB 구현전략
  * 조인 전략: 슈퍼타입과 서브타입의 PK가 같고 슈퍼타입에 서브타입의 타입을 기록하는 컬럼을 설정
  * 단일 테이블 전략: 하나의 테이블에 모든 서브타입을 넣고 서브타입의 타입을 기록
  * 구현 클래스마다 테이블 전략: 슈퍼타입 테이블은 없고 서브타입 테이블마다 속성을 모두 넣어 생성
* JPA의 기본전략은 단일 테이블 전략
* @DiscriminatorColumn 어노테이션을 슈퍼타입에 추가하면 테이블에 서브타입의 타입을 기록하는 DTYPE이라는 컬럼이 생성된다.
* 서브타입에 @DiscriminatorValue를 추가하여 DTYPE에 저장되는 값을 바꿀 수 있다. 기본값은 Entity명
* @Inheritance 에 strategy로 맵핑 전략을 선택할 수 있다.

---

### 조인 전략
* stragy=InheritanceType.JOINED
* DTYPE이 없을 수 있다.

#### 장점
* 테이블이 정규화
* 테이블 외래키 무결성을 활용가능하다.
* 저장공간이 효율화된다.

#### 단점
* 조회시 조인이 많이 사용되어 성능이 저하됨.
* 조회 쿼리가 복잡.
* 데이터 저장시 INSERT SQL이 2번 호출된다.
* 단일 테이블과 비교했을 때 관리해야하는 테이블이 많아지는 것이 큰 단점.

---

### 단일 테이블 전략
* stragy=InheritanceType.SINGLE_TABLE
* DTYPE이 무조건 있어야 한다.

#### 장점
* 조인이 필요없으므로 일반적으로 조회 성능이 빠름.
* 조회 쿼리가 간단함.

#### 단점
* 자식 엔티티가 맵핑한 컬럼은 모두 null 허용
* 단일 테이블에 저장하므로 테이블이 커져 조회 성능이 느려질 수 있다.

---

### 구현 클래스마다 테이블 전략
* stragy=InheritanceType.TABLE_PER_CLASS
* 슈퍼 클래스를 abstract로 만들지 않으면 테이블이 생설될 수도 있다.
* DTYPE이 필요없어 @DiscriminatorValue를 추가해도 반응이 없다.
* 이 경우 상위 클래스타입으로 조회를 하면 union을 통해 서브타입을 모두 찾는다.
* 일반적으로 사용하지 않아야 한다.

#### 장점
* 서브타입을 명확하게 구분해서 처리할 때 효과적
* not null 제약조건을 사용 가능

#### 단점
* 여러 서브 테이블조회 시 union sql을 사용하여 성능이 안 좋음.
* 서브 테이블 통합 쿼리가 어려움.

---

## @MappedSuperclass
---
* 공통 매핑 정보가 필요할 때 사용(id, name, created, updated 등)
* 상속관계 매핑이 아니며 Entity가 아니기 떄문에 테이블과 맵핑이 되지 않음.
* 따라서 조회나 검색이 불가능
* 직접 쓸일이 없으므로 추상 클래스로 사용할 것
* Entity 클래스는 Entity나 MappedSuperclass만 상속이 가능

---

## 실전 예제
---
* 데이터가 많아지면 json으로 비정규화시키게 된다.
* 어플리케이션이 커지면 이런 부분도 고민이 필요해진다.

---

# Reference
---
- [인프런](https://www.inflearn.com/course/ORM-JPA-Basic)
