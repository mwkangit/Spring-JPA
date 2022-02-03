# Spring-JPA


## Description

본 프로젝트는 JPA의 작동 원리를 이해한 후 Database에 직접 반영하는 형식으로 구현한 것이다. JPA의 작동 원리와 속성을 이해한 후 연관관계 매핑, 프록시, 값 타입 등 이용하여 Entity를 생성한다. 데이터의 속성, 관계, 제약을 통해 알맞은 매핑을 설정 후 refactoring을 통해 더 자연스럽고 활용성 있게 Entity를 설정한다. Database는 항상 확장에 열려있어야 하므로 확장을 염두한 구조로 설정한다. H2 데이터베이스를 이용하여 데이터 저장 및 테스트를 진행한다.



------



## Environment

<img alt="framework" src ="https://img.shields.io/badge/Framework-SpringBoot-green"/><img alt="framework" src ="https://img.shields.io/badge/Language-java-b07219"/> 

Framework: `Spring Boot` 2.6.0

Project: `Maven`

Packaging: `Jar`

IDE: `Intellij`

ORM: `JPA`(Hibernate, Spring Data JPA)

DBMS: `H2-Database`

Dependencies: `Spring Data JPA`



------



## Installation



![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black) 

```
./gradlew build
cd build/lib
java -jar hello-spring-0.0.1-SNAPSHOT.jar
```



![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white) 

```
gradlew build
cd build/lib
java -jar hello-spring-0.0.1-SNAPSHOT.jar
```



## Core Feature

```java
// N : 1 경우
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "TEAM_ID")
    private Team team;

// 1 : N 양방향 경우
@ManyToOne
@JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
private Team team;

// 1 : 1 경우 - 현재 엔티티가 주인
@OneToOne
@JoinColumn(name = "LOCKER_ID")
private Locker locker;

// N : M 경우 - 단방향
@ManyToMany
@JoinTable(name = "MEMBER_PRODUCT")
private List<Product> products = new ArrayList<>();

// N : M 을 1 : N, N : 1 로 풀어 사용하기
@OneToMany(mappedBy = "member")
private List<MemberProduct> memberProducts = new ArrayList<>();
```

Entity 사이의 연관관계 매핑을 표현한 것으로 외래키를 설정하고 상황에 따라 프록시 객체를 사용하여 성능을 높이는 방법으로 코드를 작성했다. 또한, 각 속성에 대해 제약사항을 추가하여 Database를 관리할 수 있다. Database의 Query에 의존하지 않고 자바 코드 및 객체를 이용하여 Database를 관리한다. 초기에 Database 설정을 알맞게 해야 데이터 이용 및 확장 시 성능을 높이고 확장도 유연하게 처리할 수 있다.





------



## More Explanation





