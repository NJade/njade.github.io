---
title: JPA entity 맵핑
author: Njade
date: 2020-12-03 23:00:00 +0900
categories: [JPA]
tags: [JPA]
---

이 게시글은 인프런의 [김영한님의 강의](https://www.inflearn.com/course/ORM-JPA-Basic)를 보고 정리한 것입니다.
실전 예제의 팁 등을 제외한 코드는 첨부하지 않습니다. 강의를 봐주세요.

---

## 엔티티 맵핑 소개
---
* 객체와 테이블 맵핑: @Entity, @Table
* 필드와 컬럼 맵핑: @Column
* 기본키 맵핑: @Id
* 연관관계 맵핑: @ManyToOne, @JoinColumn

---

## 객체와 테이블 맵핑
---
* @Entity가 붙은 클래시는 JPA가 관리하고 엔티티라고 한다.
* JPA를 사용해서 테이블과 맵핑한다면 @Entity가 필수
* 기본 생성자는 필수 (public or protected)
* final, enum, interface, inner 클래스는 사용 불가
* 저장할 필드도 final 사용 불가
* @Entity의 name 속성
    * JPA에서 사용할 엔티티 이름
    * 기본값: 클래스 이름
    * 같은 클래스 이름이 없다면 가급적 기본값을 사용할 것.
* @Table 속성
    * name: 맵핑할 테이블 이름, 기본값: 엔티티 이름
    * catalog: DB catalog
    * schema: DB schema
    * uniqueConstraints: DDL 생성시 유니크 제약 조건 생성

---

## 데이터베이스 스키마 자동 생성
---
* DDL을 애플리케이션 실행 시점에 자동 생성
* 테이블 중심 > 객체 중심
* DB 방언을 활용해서 DB에 맞는 DDL 생성
* 이 DDL은 개발 장비에서만 사용할 것
* 생성 DDL은 운영서버에서는 사용하지 않거나 적절히 다듬은 후 사용
* hibernate.hbm2ddl.auto
    * create: DROP + CREATE
    * create-drop: create와 동일하지만 애플리케이션 종료 시 테이블 드랍
    * update: 변경문만 반영, 컬럼 추가만 반영이 됨.
    * validate: 엔티티와 정상 맵핑되었는지 체크
    * none
* DB 방언에 따라 DDL이 바뀜
* @Column(nullable = false, length = 10, unique = true) 등으로 제약조건을 추가할 수 있음.
* @Table(uniqueConstraints = (@UniqueCoonstraint(name = "NAME_AGE_UNIQUE", columnNames={"NAME", "AGE}))) > 유니크 제약조건
* DDL 생성 기능은 DDL 자동생성에만 영향을 주고 JPA 실로직에서는 사용되지 않는다.

### 주의점
* 운영장비에서는 절대 create, create-drop, update를 사용하면 안됨.
* 개발 초기에는 create or update
* test 서버는 update or validate
* staging이나 sever는 validate or none

---

## 필드와 컬럼 맵핑
---
* @Column(name = "name")과 같이 컬럼명을 설정가능하다.
* @Eumerated()를 사용하여 enum타입을 맵핑시킬 수 있다.
* @Temporal()을 통해 DATE, TIME, TIMESTAMP 세가지에 대해 맵핑이 가능하다.
* @Lob 큰 데이터를 맵핑 BLOB, CLOB emd
* @Transient 컬럼 맵핑을 하고 싶지 않을 때 사용

### Column 
* name: 컬럼 이름
* insertable, updatable: 등록, 변경 가능 여부
* nullable: not null 제약 조건을 걸어주고 검사도 해준다.
* unique: DDL에서 unique 제약조건을 걸어줌, @Column에 걸면 이름이나 복합키 조건이 안되서 잘 사용하지 않는다.
* columnDefinition: DB 맵핑 조건을 직접 걸어줄 수 있다.
* length: DDL에서 길이 제약조건을 걸어줄 수 있다.
* percision, scale: float, double 등에서 사용

### Enumerated
* enum을 맵핑
* ORDINAL(enum순서로 저장)과 STRING(문자로 저장)이 있고 ORDINAL이 기본값이다.
* 하지만 ORDINAL을 사용하지 않는 것이 좋다.

### Temporal
* 날짜에 맵핑, 최근에는 필요가 없음.
* LocalDate, LocalDateTime 등의 자바 클래스를 사용하면 알아서 해줌.

### Lob
* 지정할 수 있는 속성이 없음.
* String, Char 등은 CLOB
* 나머지는 BLOB

### Transient
* 필드에 맵핑하지 않음.
* 메모리에서만 사용하는 경우에 사용

---

## 기본키 맵핑
---
* @Id: 직접 할당 시 @Id만 사용
* @GeneratedValue: 자동 생성
    * IDENTITY: DB에 위임, MySql에서 AUTO_INCREMENT
        * JPA는 기본적으로 commit 시점에 insert sql이 실행됨.
        * 하지만 JPA의 영속성 컨텍스트는 기본키로 데이터를 관리
        * 그래서 IDENTITY설정 시 persist시에 insert 쿼리가 날라감.
        * 따라서 batch 전략이 먹히지는 않지만 성능의 큰 문제는 없음.
    * SEQUENCE: db의 sequence를 사용, @SequenceGenerator를 사용해서 명시적으로 생성도 가능
        * SEQUENCE 전략시에도 sequence값을 얻어와야 영속성 컨텍스트에 저장할 수 있으므로 persist시에 sequence를 select하는 전략을 사용
        * persist시마다 sequence를 얻어오면 네트워크를 계속 사용하는 문제가 있어 우선적으로 증가개만큼 가져오는 전략을 사용
        * 메모리에서 우선적으로 땡겨오기 때문에 메모리가 날라가거나 하는 경우 sequence에 구멍이 생길 수 있음.
    * TABLE: 키 생성 전용 테이블을 하나 만들어서 시퀀스를 흉내, 모든 DB에 사용이 가능하지만 성능이 안 좋음. @TableGenerator가 존재
        * sequence와 거의 동일
    * SEQUENCE와 TABLE의 경우 여러 어플리케이션을 띄워도 문제가 없음. 1번 서버는 1~50, 2번 서버는 51~100, ... 으로 할당

### 식별자 전략
* 기본키 제약 조건: null x, 유일, 변하면 안된다.
* 미래까지 이 조건을 만족하는 자연키느 찾기 어려움.
* 따라서 대체키를 사용하는 것이 적절하다. (AUTO_INCREMENT나 SEQEUNCE)

---

## 실전 예제
---
* DB 제약조건, 인덱스 등의 설정은 코드 상에 length, table에 인덱스 등을 표기하는 것이 좋다.
* spring boot를 물려 사용하는 경우 column이름을 DB 관례 등으로 처리해준다.
* entity에 외래키를 넣는 데이터 중심 설계는 객체 그래프 탐색이 불가능하고 참조도 없으므로 UML도 잘못된 것이다.

---

# Reference
---
- [인프런](https://www.inflearn.com/course/ORM-JPA-Basic)
