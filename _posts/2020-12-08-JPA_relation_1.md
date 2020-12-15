---
title: JPA Relation 1
author: Njade
date: 2020-12-09 05:30:00 +0900
categories: [JPA]
tags: [JPA]
---

이 게시글은 인프런의 [김영한님의 강의](https://www.inflearn.com/course/ORM-JPA-Basic)를 보고 정리한 것입니다.
실전 예제의 팁 등을 제외한 코드는 첨부하지 않습니다. 강의를 봐주세요.

---

## 연관관계 맵핑 기초
---
* 객체의 참조와 테이블의 외래키를 맵핑
* 방향: 단방향, 양방향
* 다중성: 다대일, 일대다, 일대일, 다대다
* 연관관계의 주인

* Entity를 중심으로 ManyToOne, OneToMany를 선언
* ManyToOne 선언시 @JoinColumn을 통해 FK 선언이 필요
* OneToMany 선언시 mappedBy property를 상대 entity의 이름으로 선언해주어야 함.

```java
public class Member {
    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}

public class Team {
    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();
}
```

### 테이블 중심 모델링
* 객체를 테이블에 맞춰 객체가 FK를 가지고 있도록 데이터 중심으로 설계하면 협력관계를 만들 수 없다.
* 테이블은 FK로 join해서 연관 테이블을 찾고 객체는 참조를 사용해서 연관객체를 찾는다.

### 객체 지향 모델링
* 객체에 참조 클래스를 넣어 연관관계를 맵핑

---

## 양방향 연관관계와 연관관계의 주인
---
* 객체는 단방향 연관관계가 2개이다.

```java
a.getB();
b.getA();
```

* 테이블은 양방향 연관관계로 표현할 수 있다. > 키 하나로 조인을 통해 조회가 가능하다.
* 이 차이로 인해 두 객체 중 어떤 값을 통해 테이블을 업데이트할지 혼란이 생긴다.
* 위 a와 b에서 a.setB()와 b.setA() 중 어떤 것이 테이블에 영향을 줄지 명확하지 않다.
* 이 문제 해결을 위해 두 객체 중 하나를 주인으로 지정
* 주인은 등록과 수정이 가능하며 주인이 아닌 쪽은 읽기만 가능하다.
* 주인은 mappedBy를 사용해서는 안되고 주인이 아니면 mappedBy로 주인을 지정
* 외래키가 있는 곳을 주인으로 정한다.
* 만약 외래키가 없는 객체를 주인으로 설정하면 객체 수정 시 맵핑되지 않은 테이블로 쿼리가 나간다.
* a에 외래키가 있는데 b를 주인으로 설정하면 b.setA()를 하면 a 테이블로 쿼리가 나감.
* DB 입장에서 외래키가 있는 곳이 N이고 Entity 입장에서 ManyToOne이고 이것을 주인으로 처리하는 편이 좋다.

### 주의점
* 연관관계 주인에 값을 업데이트하지 않음.
* 객체관계를 고려하면 양쪽 객체에 모두 값을 업데이트해주는 것이 맞다.
* 업데이트를 하지 않고 flush와 clear를 하지 않으면 1차 캐시에 업데이트되지 않은 값이 나와 잘못된 결과를 얻을 수 있다.
* 또 JPA를 사용하지 않는 테스트를 고려하더라도 양방향으로 셋팅하는 것이 적절하다.
* 객체에서 데이터 변경 method(연관관계 편의 method)에서 양방향으로 업데이트하도록 해주는 것이 적절하다.
* 연관관계 편의 method는 한 쪽만 생성하는 것이 좋다.
* 양방향 매핑시에 무한 루프가 걸리게 될 수 있다.
* toString()을 method를 자동생성하면 서로 호출해서 문제가 된다. 

```java
class A {
    ...
    public Stirng toString() {
        return "b = " + b; 
    }
}

class B {
    ...
    public String toString() {
        return "a = " + a;
    }
}
```

* Entity를 컨트롤러에서 직접반환해버리면 jackson에서 무한루프에 빠진다.

---

## 정리
---
* 처음 설계시 단방향 맵핑에서 설계가 끝나야 한다.
* 양방향 맵핑은 반대 방향 조회가 추가된 것 뿐이다.
* 즉 단방향 맵핑만 잘해두면 테이블에 영향을 주지 않기 때문에 필요할 때 양방향 맵핑은 추가하면 된다. 

---

## 실전 예제
---
* 설계시 연관관계를 잘 끊어내는게 중요함.
* 어디부터 시작할 것인지 생각해보기.
* 고객이 주문한 내역인 경우, 고객부터 시작하는 것이 아닌 주문부터 시작하면 되기 때문에 고객 Entity에 주문내역이 필요하지 않음.
* List 사용시 ArrayList등으로 초기화해주는 것이 NPE 등을 방지해줄 수 있기 때문에 좋음.
* 양방향은 편의성때문이지 필수사항이 아님.

---

# Reference
---
- [인프런](https://www.inflearn.com/course/ORM-JPA-Basic)
