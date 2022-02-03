# JPA Background



- #### SQL로 데이터베이스 로직에 CRUD 방식을 적용하기에는 너무 많은 코드가 필요하다. 또한, 데이터베이스 스키마 수정 시 유지보수를 해결하기 쉽지 않다.

- #### 모든 객체를 미리 로딩할 수 없어서 계층 분할이 어려웠다. 또한, 쿼리를 하나의 인스턴스에 너무 많이 날리게 되어 불필요한 지연이 발생한다.

- #### Collection에 저장하는 방식으로 데이터베이스에 의존적이지 않은 객체 지향형으로 데이터를 관리하기 위해 Hibernate가 탄생했다.

- #### Hibernate를 자바 표준으로 인정하고 발전하여 JPA 가 탄생했다.



![JPA Logic](https://user-images.githubusercontent.com/79822924/152362538-3d58d9e8-bbcd-4d2a-b551-cfd7d0bc3739.png)

- #### JPA는 트랜잭션 내에서 이루어지며 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장한다.

- #### 1차 캐시는 영속성 컨테이너를 운영하는 것이다. 즉, 한번 사용한 쿼리를 중복하여 사용하지 않도록 캐시를 운영하여 바로 이용 가능하게 한다.

- #### 쓰기 지연을 사용하여 트랜잭션 커밋 시 컨테이너에 모아놓은 쿼리를 한번에 데이터베이스에 전송할 수 있다.

- #### 지연로딩은 객체가 실제 사용될 때 로딩되는 것이며 즉시로딩은 JOIN SQL로 한번에 연관된 객체까지 미리 조회하는 것이다.



# JPA Setting



```xml
<!--persistence.xml-->

<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/ORM"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <property name="hibernate.jdbc.batch_size" value="10"/>
            <property name="hibernate.hbm2ddl.auto" value="create" />
        </properties>
    </persistence-unit>
</persistence>
```

```xml
<!--pom.xml-->

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>jpa-basic</groupId>
    <artifactId>ex1-hello-jpa</artifactId>
    <version>1.0.0</version>

    <properties>
        <maven.compiler.source>15</maven.compiler.source>
        <maven.compiler.target>15</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- JPA 하이버네이트 -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>5.3.10.Final</version>
        </dependency>
        <!-- H2 데이터베이스 -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>1.4.200</version>
        </dependency>
        <!-- java 11 -->
        <dependency>
            <groupId>javax.xml.bind</groupId>
            <artifactId>jaxb-api</artifactId>
            <version>2.3.0</version>
        </dependency>
    </dependencies>

</project>
```

- #### Pom.xml을 이용하여 JPA 버전, 사용할 데이터베이스 연동, 자바 버전을 설정할 수 있다.

- #### Persistence.xml은  resource 디렉토리의 META-INF 내부에 작성한다. 데이터베이스 방언과 JPA 속성을 설정할 수 있다.



# JPA Working System



![JPA 구동 방식](https://user-images.githubusercontent.com/79822924/152362596-699917c9-d135-4d05-bf49-54a73415f2a1.png)

- #### EntityManagerFactory는 하나만 생성하여 어플리케이션 전체에 공유한다.

- #### 엔티티 매니저는 쓰레드간 공유하면 안돼며 JPA의 모든 데이터 변경은 트랜잭션 안에서 실행되어야 한다.

- #### 어플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검색 조건이 필요한 SQL이 필요하다. 이러한 SQL을 지원한는 것이 JPQL이다.

- #### JPQL은 SQL을 추상화한 것으로 객체 지향 쿼리 언어이다.

- #### 단, SQL은 DB 테이블을 대상으로 쿼리하지만 JPQL은 엔티티 객체를 대상으로 쿼리한다. 즉, DB SQL에 의존하지 않게 할 수 있다.



# Persistence Management



## Persistence Context



![엔티티 매니저 팩토리와 엔티티 매니저](https://user-images.githubusercontent.com/79822924/152362661-d519ebd7-7e1c-4c75-b9cc-5f7eb2836632.png)

- #### 엔티티 매니저 팩토리는 유일하며 각 사용자마다 엔티티 매니저를 할당한다. 즉, 각 트랜잭션 마다 엔티티 매니저를 할당한다.

- #### 엔티티 매니저를 통해 영속성 컨텍스트에 접근 가능하다.

- #### 영속성 컨텍스트는 논리적인 개념으로 눈에 보이지 않는다.



![엔티티의 생명주기](https://user-images.githubusercontent.com/79822924/152362697-7ff7fa3a-be89-42a1-b229-d6633a5f4e20.png)

- #### 비영속(new/transient)는 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태로 메모리에서 사용하는 변수의 개념으로 생각할 수 있다.

- #### 영속(managed)는 영속성 컨텍스트에서 관리되는 상태이다. Persist()를 통해 영속성 컨텍스트로 등록할 수 있다.

- #### 준영속(detached)은 영속성 컨텍스트에 저장되었다가 분리된 상태이다. Detach() 로 영속성 컨텍스트에서 분리할 수 있다.

- #### 삭제(removed)는 삭제된 상태이다. Remove() 로 객체를 삭제할 수 있다.



## Persistence Context Advantage



![1차 캐시 이점](https://user-images.githubusercontent.com/79822924/152362723-b7bb135d-e806-4fe3-9281-ed2285b441ad.png)

- #### 한 트랜잭션에서 영속성 컨텍스트에 등록시 1차 캐시에 등록되며 다시 특정 객체를 사용하고자하면 1차 캐시에서 먼저 객체를 탐색하여 사용한다.

- #### Persist(), find() 등으로 1차 캐시에 객체를 등록하는 것이 가능하다.

- #### 1차 캐시로 데이터베이스에 접근하는 경우의 감소로 성능이 향상될 수 있다.



```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");
System.out.println(a == b); // true
```



- #### 1차 캐시로 반복 가능한 읽기(REPEATABLE READ) 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 어플리케이션 차원에서 제공하여 영속 엔티티의 동일성을 보장할 수 있다.



![쓰기지연](https://user-images.githubusercontent.com/79822924/152362750-8e0cdaf5-06b5-4b3b-a3a0-df512b955343.png)

- #### 영속성 컨텍스트는 쓰기 지연 저장소에 SQL 쿼리를  저장한다.

- #### Persist() 시점에는 1차 캐시, 쓰기 지연 저장소에 정보를 저장하는 것으로 아직 데이터베이스에 전송하지 않는다.

- #### Flush()로 쓰기 지연 저장소의 SQL을 데이터베이스에 전송하고 commit()으로 데이터베이스에 저장한다. Commit()으로 flush()를 모두 해결할 수 있다.

- #### Commit() 후에도 트랜잭션이 종료되지 않았으면 영속성 컨텍스트는 삭제되지 않고 그대로 남아있다.

- #### 모든 과정은 동일한 트랜잭션에서 이루어져야 한다.



![변경 감지](https://user-images.githubusercontent.com/79822924/152362788-bbf820d9-d925-4925-a836-df827d2c8aa1.png)

- #### 스냅샷은 엔티티가 영속성 엔티티에 최초로 들어온 시점의 상태이다.

- #### Flush()가 발생하면 현재 엔티티를 스냅샷과 비교하여 변경된 것에 대한 update 쿼리를 쓰기 지연 저장소에 저장한다. 하나하나 비교하여 다른 것을 찾아내는 것으로 변경 감지(Dirty Checking)이라고 한다.

- #### Update 쿼리가 반영된 후 commit()으로 데이터베이스에 저장된다.



```java
Member memberA = em.find(Member.class, “memberA");
em.remove(memberA); //엔티티 삭제
```

- #### 이미 저장된 엔티티를 조회 후 삭제하면  commit() 시점에 데이터베이스에 반영된다.

- #### Flush()는 영속성 컨텍스트의 변경내용을 데이터 베이스에 반영하는 것이다.

- #### Flush()는 변경 감지, 수정된 엔티티 쓰기 지연  저장소에 등록, 쓰기 지연 저장소의 쿼리를 데이터베이스에 전송(등록, 수정, 삭제 쿼리)할 때 발생한다.

- #### Flush()는 직접 호출할 수도 있지만 트랜잭션 commit(), JPQL 쿼리 실생 시점에 자동으로 호출 된다.



```java
em.setFlushMode(FlushModeType.COMMIT);
```

- #### FlushModeType.AUTO는 default 값으로 commit이나 쿼리를 실행할 때 자동으로 flush()되도록 한다.

- #### FlushModeType.COMMIT은 commit()할 때에만 flush()가 발생하게 하는 것이다.

- #### Flush()는 영속성 컨텍스트를 비우지 않으며 영속성 컨텍스트의 변경내용을 데이터베이스에 동기화 할 뿐이다.

- #### Commit() 직전에 반드시 flush()를 실행해야 한다.

- #### Em.detach(entity)를 통해 특정 엔티티를 준영속 상태로 만들 수 있다.

- #### Em.clear()를 통해 영속성 컨텍스트를 완전히 초기화 할 수 있다.

- #### Em.close()를 통해 영속성 컨텍스트를 종료할 수 있다.



# Entity Mapping



## Entity

- #### @Entity가 붙은 클래스는 JPA가 관리하며 엔티티라고 부른다.

- #### Reflection, proxy를 위해 기본 생성자는 public이나 protected로 필수적으로 만들어야 한다.

- #### Final 클래스, enum, interface, inner 클래스에는 적용할 수 없으며 DB에 저장할 필드에 final 키워드를 사용할 수 없다.

- #### Name 속성으로 JPA에서 사용할 이름을 지정할 수 있다. Default 값은 클래스 이름을 그대로 사용하는 것이다.



## Table

- #### @Table은 엔티티와 매핑할 테이블을 지정한다.

- #### Name 속성은 매핑할 테이블 이름을 지정한다. Default 값은 엔티티 이름을 사용한다.

- #### Catalog 속성은 데이터베이스 catalog에 매핑한다.

- #### Schema 데이터베이스 schema에 매핑한다.

- #### UniqueConstraints(DDL) 속성은 DDL 생성 시에 유니크 제약 조건을 생성하는 것이다.



## Database Schema Auto Creation



```xml
<property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

<property name="hibernate.hbm2ddl.auto" value="create" />
```

- #### DDL을 어플리케이션 실행 시점에 자동 생성하는 것이 가능하다.

- #### 이러한 방식으로 생성한 DDL은 개발 장비에서만 사용하며 운영에는 사용하면 안됀다.

- #### Persistence.xml에서 DB 방언을 선택할 수 있으며 데이터베이스 자동 생성 속성을 지정할 수 있다.

- #### DDL 자동 생성 방식에는 여러 속성이 존재한다.

  - #### Create 속성은 기존 테이블을 삭제후 다시 생성하는 것이다(DROP + CREATE).

  - #### Create-drop 속성은 create와 같으나 종료시점에 테이블을 drop한다.

  - #### Update 속성은 변경부분만 반영이 되지만 운영 DB에는 사용하면 안됀다(Column 삭제는 불가하다).

  - #### Validate 속성은 엔티티와 테이블이 정상 매핑되었는지만 확인한다. 즉, column 관계가 모두 일치하는지 확인하는 것이다.

  - #### None 속성은 아무것도 사용하지 않겠다는 것이다.

- #### 보통 개발 초기 단계에는 local로 create, update로 시작하며 테스트 서버에서는 여러 사람이 사용하므로 update, validate를 사용한다. 그리고 스테이징과 운영 서버는 validate, none을 사용한다.



```java
@Column(nullable = false, length = 10)
```

- #### 제약조건을 추가할 수 있다. 회원 명은 필수이며 10자를 넘으면 안돼는 조건이다.

```java
@Table(uniqueConstraints = {@UniqueConstraint(name = "NAME_AGE_UNIQUE", columnNames = {"NAME", "AGE"})})
```

- #### 유니크 제약조건을 추가하는 것이 가능하다.

- #### 즉, DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고 JPA의 실행 로직에는 영향을 주지 않는다.



## Field & Column Mapping



```java
	@Id
	private Long id;
	
	@Column(name = "name")
	private String username;

	private Integer age; // 가장 적절한 숫자타입 만들어서 부여한다.

    @Enumerated(EnumType.STRING) // db에는 enum 타입이 없어서 @Enumerated를 붙여준다.
    private RoleType roleType;

    @Temporal(TemporalType.TIMESTAMP) // 타입에 Date, Time, TimeStamp가 있다. TimeStamp는 둘다 사용한 것이다.
    private Date createdDate;

    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;

    @Lob  Clob, Blob 사용 가능하다. VARCHAR을 넘는 큰 컨텐츠를 입력하고 싶으면 @Lob을 사용한다. // @Lob 시 자료형이 문자타입이면 clob 실행한다.
//    private String description;
```

- #### 기본 매핑 어노테이션에는 여러 종류가 존재한다.

  - #### @Column 어노테이션은 컬럼에 매핑하는 것이다.

  - #### @Temporal 어노테이션은 날짜 타입에 매핑하는 것이다.

  - #### @Enumerated 어노테이션은 enum 타입에 매핑하는 것이다

  - #### @Lob 어노테이션은 BLOB, CLOB을 매핑한다.

  - #### @Transient 어노테이션은 특정 필드를 컴럼에 매핑하지 않는 것으로 매핑을 무시하는 것이다. 즉, DB와 상관없는 변수를 memory에서만 사용하는 것이다.

- #### @Column 어노테이션에는 여러 속성이 존재한다.

  - #### Name 속성은 필드와 매핑할 테이블의 컬럼 이름을 지정할 수 있다. Default 값은 객체의 필드 이름이다.

  - #### Insertable, Updatable 속성은 등록, 변경 가능 여부를 결정한다. Default 값은 TRUE이다.

  - #### Nullable(DDL) 속성은 null 값의 허용 여부를 설정한다. False로 설정하면 DDL 생성 시에 not null 제약조건이 붙는다.

  - #### Unique(DDL) 속성은 @Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용한다. 보통 table에 사용해야 정상적인 실행 콘솔 확인이 가능하다.

  - #### ColumnDefinition(DDL) 속성은 데이터베이스 컬럼 정보를 직접 줄 수 있다. Default 값은 필드의 자바 타입과 방언 정보를 사용한다. varchar(100) default 'EMPTY'와 같은 정보를 부여할 수 있다.

  - #### Length(DDL) 속성은 문자 길이 제약조건, String 타입에만 사용한다. Default 값은 255이다.

  - #### Precision, scale(DDL) 속성은 BigDecimal 타입과 같은 아주 큰 숫자나 소수 사용시 사용하며 BigInteger도 사용 가능하다. Precision은 소수점을 포함한 전체 자릿수를, scale은 소수의 자릿수이다. 참고로 double, float 타입에는 적용되지 않는다.

- #### @Enumerated에는 하나의 속성이 있다.

  - #### Value 속성은 EnumType.ORDINAL, EnumType.STRING이 있으며 ORDINAL은 enum 순서를 데이터베이스에 저장하며 STRING은 enum 이름을 데이터베이스에 저장한다. Default 값은 ORDINAL이다. ORDINAL은 수정 시 값의 순서에 따라 변화할 수 있으므로 위험하다. STRING을 사용하는 것이 좋다.

- #### @Temporal은 날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용한다. 참고로 LocalDate(연 월), LocalDateTime(연 월 시)을 사용할 때에는 생략 가능하다.

  - #### Value 속성은 TemporalType.DATE, TemporalType.TIME, TemporalType.TIMESTAMP 가 있는데 DATE는 날짜, 데이터베이스 date 타입과 매핑하고 TIME은 시간, 데이터베이스 time 타입과 매핑하고 TIMESTAMP는 날짜와 시간, 데이터베이스 timestamp 타입과 매핑한다.

- #### @Lob은 데이터베이스 BLOB, CLOB 타입과 매핑한다. @Lob에는 지정할 수 있는 속성이 없으며 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑을 한다.

  - #### CLOB : String, char[], java.sql.CLOB

  - #### BLOB : byte[], java.sql.BLOB

- #### @Transient는 필드에 매핑하지 않고 데이터베이스에 저장 및 조회 기능을 사용하지 않는 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용한다.

  - #### @Transient

    #### private Integer temp;



## Primary Key Mapping



```java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```

- #### 기본키 직접 할당 시 @Id만 사용한다.

- #### 기본키 자동 생성 시 @GeneratedValue와 속성을 이용한다.

  - #### IDENTITY : 데이터베이스에 위임한다. 주로 MYSQL 에 사용한다.

  - #### SEQUENCE : 데이터베이스 시퀀스 오브젝트를 사용한다. 주로 ORACLE에 사용하며 @SequenceGenerator이 필요하다.

  - ####  TABLE : 키 생성용 테이블을 사용하며 모든 DB에서 사용 가능하다. @TableGenerator이 필요하다.

  - #### AUTO : 방언에 따라 자동으로 지정한다. @GeneratedValue의 default 값이다.



```java
@Entity
public class Member {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
}
```

- #### IDENTITY는 기본키 생성을 데이터베이스에 위임하는 것으로 MYSQL, PostgreSQL, SQL Server, DB2에서 사용하며 MYSQL의 AUTO_INCREMENT로 볼 수 있다.

- #### 쿼리에 NULL값 주면 데이터베이스에서 알아서 기본키를 계산하여 생성한다.

- #### 영속성 관리를 하려면 기본키가 있어야 하지만 IDENTITY는 기본키가 데이터베이스에 도달해서 만들어진다. 즉, 영속성 관리를 위해 persist 시점에 바로 쿼리가 데이터베이스로 전송된다.

- #### IDENTITY는 쿼리를 즉시 전송하여 지연쓰기 전략이 불가능하다.



```java
@Entity
@SequenceGenerator(
name = “MEMBER_SEQ_GENERATOR",
sequenceName = “MEMBER_SEQ", //매핑할 데이터베이스 시퀀스 이름
initialValue = 1, allocationSize = 1)
public class Member {
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ_GENERATOR")
	private Long id;
}
```

- #### SEQUENCE는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트로 ORACLE, PostgreSQL, DB2, H2 Database에서 사용한다.

- #### 시퀀스는 String, Integer이 아닌 Long으로 사용하는 것이 좋다.

- #### 시퀀스도 IDENTITY와 동일하게 초기에 기본키 값이 없어서 영속성으로 관리하지 못한다. 성능 최적화를 위해 첫 쿼리 전송 시 할당한 사이즈 만큼 시퀀스를 가져온 후 사이즈 만큼의 시퀀스를 이용한다. 만약 사이즈를 모두 사용했다면 쿼리가 다시 전송되어 다음 사이즈만큼 시퀀스를 가져와서 사용한다. 즉, 미리 데이터베이스에 할당 사이즈 만큼 시퀀스를 생성하는 것이다.

- #### 테이블마나 시퀀스를 따로 관리하려면 @SequenceGenerator로 매핑해야 한다.

- #### @SequenceGenerator에는 여러 속성이 존재한다.

  - #### Name 속성은 식별자 생성기 이름을 부여하는 것으로 필수이다.

  - #### SequenceName 속성은 데이터베이스에 등록되어 있는 시퀀스 이름이다. Default  값은 hibernate_sequence이다.

  - #### InitialValue 속성은 DDL 생성 시에만 사용하며 시퀀스 DDL을 생성할 때 처음 1 시작하는 수를 지정한다. Default 값은 1이다.

  - #### AllocationSize 속성은 시퀀스 한 번 호출에 증가하는 수로 성능 최적화에 사용한다. 데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다. Default 값은 50이다.

  - #### Catalog, Schema 속성은 데이터베이스 catalog, schema 이름에 매핑한다.



```java
@Entity
@TableGenerator(
name = "MEMBER_SEQ_GENERATOR",
table = "MY_SEQUENCES",
pkColumnValue = “MEMBER_SEQ", allocationSize = 1)
public class Member {
	@Id
	@GeneratedValue(strategy = GenerationType.TABLE, generator = "MEMBER_SEQ_GENERATOR")
	private Long id;
}
```

- #### TABLE은 키 생성 전용 테이블을 생성하여 데이터베이스 시퀀스를 흉내내는 전략이다.

- #### 모든 데이터베이스에 적용 가능한 것이 장점이지만 테이블을 직접 사용하여 Lock이 가능하고 최적화가 불가능하여 성능이 다른 전략에 비해 좋지 않을 수 있다.

- #### TABLE은 실제 시퀀스 테이블을 생성한 후 next-value로 기본키 값을 가져온다.

- #### @TableGenerator에는 여러 속성이 존재한다.

  - #### Name 속성은 식별자 이름을 지정하며 필수이다.

  - #### Table 속성은 키 생성 테이블명을 부여한다. Default 값은 hibernate_sequences이다.

  - #### PkColumnName 속성은 시퀀스 컬럼명을 부여한다. Default 값은 sequence_name이다.

  - #### ValueColumnName 속성은 시퀀스 값 컬럼명이다. Default 값은 next_val이다.

  - #### PkColumnValue 속성은 키로 사용할 값의 이름을 부여한다. Default 값은 엔티티 이름이다.

  - #### InitialValue 속성은 초기 값으로 마지막으로 생성된 값이 기준이다. Default 값은 0이다.

  - #### AllocationSize 속성은 시퀀스 한 번 호출에 증가하는 수 이다. Default 값은 50이다. 여러 서버가 한 데이터베이스를 이용해도 미리 할당 크기를 정해놨으므로 오류가 발생하지 않는다.

  - #### Catalog, Schema 속성은 데이터베이스 catalog, schema 이름을 지정한다.

  - #### UniqueConstraint 속성은 유니크 제약 조건을 지정한다.

- #### 기본키 제약조건은 NULL이 아니어야 하며 유일하고 변하면 안됀다.

- #### 비즈니스를 키로 가져오는 것은 매우 위험한 행동으로 generatedvalue, random 값과 같은 비즈니스와 관련없는 값을 사용해야 한다. 예를 들어, 주민등록번호는 기본키로 사용하면 안됀다. 다른 테이블에서 외래키로 사용하다가 모두 바뀌는 일이 발생할 수 있다.

- #### 권장하는 규격은 Long형 + 대체키 + 키 생성전략을 사용하는 것이다.



# Association Mapping

- #### 객체를 테이블에 맞추어 데이터 중심으로 모델링하면 협력 관계를 만들 수 없다.

- #### 테이블은 외래키로 조인을 사용해서 연관된 테이블을 찾는다.

- #### 객체는 참조를 사용해서 연관되 객체를 찾는다.



## One-Way Association



![단방향 연관관계](https://user-images.githubusercontent.com/79822924/152362854-e1735aaa-0b6c-4231-8269-a305d2ffcfbb.png)

```java
@Entity
public class Member {

    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;
    
    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}
```

- #### Member 엔티티에 Team 엔티티의 Id가 외래키로 있고 N : 1 경우 Member이 연관관계 주인이 되어 @ManyToOne 어노테이션으로 외래키를 매핑한다.

- #### Find()를 사용하면 연관된 속성도 조회할 수 있다.

- #### 연관관계 수정 시 수정할 엔티티를 미리 persist 후 collection 방식으로 수정하면 된다.



## Bidirectional Association



![양방향 연관관계](https://user-images.githubusercontent.com/79822924/152362880-1f6bdc0c-8754-4b1a-b9e6-712fc6e3fac5.png)

![양방향 연관관계2](https://user-images.githubusercontent.com/79822924/152362896-ea3f717c-ff71-4a0e-9c4a-804d9fa94cde.png)

```java
@Entity
public class Team{
	
	@Id
	@GeneratedValue
	private Long id;
	
	private String name;
	
	@OneToMany(mappedBy = "team")
	List<Member> members = new ArrayList<Member>();
}
```

- #### Team 엔티티 기준 1 : N 이면서 Member의 team 변수와 매핑 되었다는 뜻이다.

- #### ArrayList로 객체를 생성해야 add할 때 NullPoint가 발생하지 않는다.

- #### 양방향 매핑은 엔티티가 양쪽에서 참조하여 사용 가능해야할 때 사용한다.

- #### 테이블은 사실상 외래키로 연결되어 join하면 되기 때문에 외래키 생성 시 이미 양방향이라고 할 수 있다.



##  Association Master & MappedBy



- #### 객체의 양방향 관계는 사실 양방향 관계가 아니라 서로 다른 단방향 관계 2개이다.

- #### 테이블은 외래키 하나로 두테이블을 join할 수 있어서 외래키 하나로 양방향 관계가 성립한다.

- #### 연관관계의 주인은 외래키가 있는 엔티티로 해야 한다.

- #### 연관관계의 주인만이 외래키를 관리할 수 있다(등록, 수정).

- #### 주인이 아닌쪽은 읽기만 가능하다.

- #### MappedBy는 어떠한 것의 주인이 아닌 뜻으로 주인은 mappedBy 속성을 사용하지 않는다. 즉, 주인이 아니면 mappedBy 속성으로 주인을 지정한다.



```java
Team team = new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setName("member1");

team.getMembers().add(member);
member.setTeam(team); 
em.persist(member);
```

- #### 양방향 매핑시 연관관계의 주인에 값을 입력해야 한다.

- #### 순수한 객체 관계를 고려하면 항상 양쪽 모두 값을 입력해야 한다.



```java
public void changeTeam(Team team) {
        this.team = team;
        team.getMembers().add(this); // 현재 이 객체를 team의 members에 추가한다.
    }
```

- #### 연관관계 편의 메소드를 엔티티에 생성하여 양쪽에 값을 설정할 수 있다.

- #### 양방향 매핑 시 toString(), lombok, JSON으로 무한 루프 및 오류가 발생할 수 있다.

  - #### toString(), lombok :  양쪽의 엔티티가 각각 상대방의 toString() 혹은 lombok 메소드를 호출하여 무한 루프에 빠질 수 있다.

  - #### JSON : 양방향 연관관계일 경우 엔티티를 JSON으로 임의로 바꿔버리면 무한 루프에 빠진다. Controller은 엔티티를 절대 반환하면 안됀다. 엔티티가 중간에 변경되면 API 스펙이 변하게 된다. 엔티티 반환 시 단순하게 값만 있는 DTO로 변환 후 반환한다.

- #### 단방향 매핑으로 이미 연관관계는 매핑된 것이다.

- #### 양방향 매핑은 반대 방향으로 조회 기능만 추가되는 것 뿐이다.

- #### 처음 개발 할 때에는 단방향으로 생성한다.

- #### JPQL에서 역방향으로 탐색할 일이 많아질 경우 양방향 매핑을 생성한다.

- #### 비즈니스 로직을 기준으로 연관관계의 주인을 선택하면 안돼고 연관관계의 주인은 반드시 외래키의 위치를 기준으로 정해야 한다. 주인이 아닌 쪽에 객체를 설정할 일이 있다면 연관관계 편의 메소드를 사용한다.



# Variety of Association Mapping



## N : 1



![N 대 1 단방향](https://user-images.githubusercontent.com/79822924/152362954-a6384d99-01ed-407b-8a93-6b5f531a9e72.png)

![N 대 1 양방향](https://user-images.githubusercontent.com/79822924/152362971-bfb9fd7e-050f-474c-9799-3aba6b513198.png)

- #### N : 1 은 가장 많이 사용하는 연관관계 구조이다.

- #### 양방향 연관관계 시 외래키가 있는 곳을 주인으로 한다.



## 1 : N 



![1 대 N 단방향](https://user-images.githubusercontent.com/79822924/152362991-959114cc-10ce-4136-b190-f00667dd0a42.png)

![1 대 N 양방향](https://user-images.githubusercontent.com/79822924/152363014-053b63c3-4dfe-4d76-a968-8e2ea8e25190.png)

- #### 1 : N 연관관계에서 외래키가 상대 엔티티에 있어도 1이 연관관계 주인이 된다.

- #### 테이블의 1 : N 관계는 항상 N 엔티티에 외래키가 있다.

- #### 객체와 테이블의 차이 때문에 반대편 테이블의 외래키를 관리하는 특이한 구조로 억지로 맞추는 듯한 느낌이 발생한다.

- #### @JoinColumn 어노테이션을 반드시 사용해야 한다. @JoinColumn이 없다면 @JoinTable 방식으로 중간에 테이블 하나를 추가해야 한다. 즉, @JoinTable의 default로 주인엔티티_상대엔티티 테이블을 생성하여 외래키를 받는 구조로 생성한다.

- #### MappedBy는 사용하지 않는다.

- #### 위의 구조에서는 member 리스트를 업데이트하면 Member테이블의 TEAM_ID(외래키)를 업데이트 하는 것이다.

- #### 만약 테이블에서 N 엔티티에 외래키가 없고 1 엔티티에 외래키가 있으면 insert 시 memberID와 중복 가능성이 있다.

- #### 1 : N 연관관계는 외래키가 다른 테이블에 있으므로 추가로 UPDATE SQL을 실행해야하고 상대의 값을 업데이트  하기 때문에 유지보수가 매우 좋지 않다. 즉, 1 : N보다 N : 1 연관관계를 사용하는게 유지보수에 좋다.

- #### 1 : N 양방향 매핑은 공식적으로 존재하지 않는다. 임의로 설정하는 구조이다.

- #### 1 : N 양방향 매핑 사용 시 @JoinColumn(insertable = false, updatable = false)를 사용하여 읽기 전용으로 조회만 가능하게 만든다.

  - #### Insertable = false : insert가 불가하게 하는 것이다.

  - #### Updatable = false : update가 불가하게 하는 것이다.

- #### 즉, 1 : N 양방향 매핑은 읽기 전용 필드를 사용해서 양방향처럼 사용하는 방법이다.



## 1 : 1



![1 대 1 단방향](https://user-images.githubusercontent.com/79822924/152363058-7aa79465-b159-4c48-b731-6deb121a0796.png)

![1 대 1 양방향](https://user-images.githubusercontent.com/79822924/152363064-95942a62-7810-47bf-b7c3-27591fcf5454.png)

- #### 1 : 1 관계는 반대도 1 : 1 관계이다.

- #### 주 테이블이나 대상 테이블 중에 아무 곳에나 외래키를 선택해도 되지만 많이 사용되는 주 테이블에 외래키를 설정하는 것이 편하다.

- #### 외래키에 데이터베이스 유니크(UNIQUE) 제약조건을 추가해야 데이터베이스 입장에서 관리가 쉽다.

- #### N : 1 처럼 외래키가 있는 곳이 연관관계 주인이며 반대편은 mappedBy로 매핑한다.

- #### 1 : 1 연관관계에서 외래키 가진 테이블과 객체 엔티티가 다르면 단방향 관계를 JPA가 지원하지 않는다. 양방향 관계는 성립하며 주 테이블을 가질 때의 양방향 연관관계와 동일한 구조이다.

- #### DBA 와 개발자 입장에서 외래키 설정 위치에 따라 입장이 다르다.

  - #### DBA 입장 : Member이 여러개의 Locker을 가질 수 있다면 연관관계가 1 : N이 된다. 그러므로 1 : 1 연관관계를 1 : N으로 수정 시 Locker에 외래키가 있었던 것이 좋다.

  - #### Developer : 성능 측면으로 Member 조회 경우가 많다면 Member 조회로 Locker까지 조회할 수 있기 때문에 Locker 유무 로직 활용 시 효율적이다. 즉, 객체 지향형 입장에서는 Member에 외래키가 있었던 것이 좋다.

- #### 주 테이블에 외래키를 설정할 때의 장점, 단점이 있다.

  - #### 장점 : 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인 가능하다.

  - #### 단점 : 값이 없으면 외래키에 NULL을 허용해야 하므로 DBA입장에서 치명적이다.

- #### 대상 테이블에 외래키를 설정할 때의 장점, 단점이 있다.

  - #### 장점 : 주 테이블과 대상 테이블을 1 : 1 관계에서 1 : N 관계로 변경 시 테이블 구조를 유지하기 쉽다.

  - #### 단점 : 프록시 기능의 한계로 지연 로딩으로 설정해도 항상 즉시 로딩된다. 원래 NULL이면 NULL저장하고 아니면 포록시를 넣지만 이미 쿼리를 전송하여 즉시 로딩으로 설정되어 지연 로딩이 쓸모 없어진다. 그리고 반드시 1 : 1 양방향 관계로 생성해야 한다. Developer 입장에서 신경쓸 사항이 많다.



## N : M



![N 대 M 연관관계 객체](https://user-images.githubusercontent.com/79822924/152363117-1d59bb5e-066d-489d-ba03-e97a74d1494a.png)

![N 대 M 연관관계 테이블](https://user-images.githubusercontent.com/79822924/152363143-d2e334d6-74ed-46bc-b01d-7759bd9b6c69.png)

![N 대 M to 1 대  N, N 대 1 객체](https://user-images.githubusercontent.com/79822924/152363166-99f2e43b-fbec-4f16-8caa-5c8bd4003a53.png)

![N 대 M to 1 대 N, N 대 1 테이블](https://user-images.githubusercontent.com/79822924/152363186-840853ea-06e3-4066-97ec-5f454f0c27ec.png)

```java
@ManyToMany
@JoinTable(name = "MEMBER_PRODUCT")
private List<Product> products = new ArrayList<>();
```

- #### 관계형 데이터베이스는 정규화된 테이블 2개로 N : M 관계를 표현할 수 없어서 연결 테이블을 추가하여 1 : N, N : 1 관계로 풀어내야 한다.

- #### 객체는 컬렉션을 사용해서 객체 2개로 N : M 관계를 매핑하는 것이 가능하다.

- #### @ManyToMany 어노테이션을 설정 후 @JoinTable로 연결 테이블을 지정하여 단방향, 양방향 관계를 설정해줄 수 있다.

- #### 하지만 연결 테이블에 다른 속성을 추가할 수 없는 단점이 있다. 즉, 연결 테이블에 매핑 정보인 외래키만 들어가고 추가로 다른 정보는 들어갈 수 없어서 사용할 때 힘들다.

- #### N : M 관계는 실무에서 절대 사용하면 안돼는 관계 구조이다.

- #### N : M 관계를 구현해야 한다면 연결 테이블용 엔티티를 추가하는 것이 좋다. 즉, 클래스를 하나 더 만들어서 양쪽의 외래키를 설정한 후 추가 정보를 설정할 수 있다. JPA에서 2개 기본키를 묶어서 만들면 composite ID가 필요하므로 대체키로 의미없는 기본키를 만들어서 관리하는 것이 좋다.



## @JoinColumn & @ManyToOne & @OnetoMany



- #### @JoinColumn은 외래키를 매핑할 때 사용하며 여러 속성이 존재한다.

  - #### Name : 매핑할 외래키 이름을 설정할 수 있다. Default 값은 필드명 + _ + 참조하는 테이블의 기본키 컬럼명 이다.

  - #### ReferencedColumnName : 외래키가 참조하는 대상 테이블의 컬럼명을 설정할 수 있다. Default 값은 참조하는 테이블의 기본키 컬럼명이다.

  - #### ForeignKey(DDL) : 외래키 제약조건을 직접 지정할 수 있다. 이 속성은 테이블을 생성할 때만 사용한다.

  - #### Unique, nullable, insertable, updatable, columnDefinition, table : @Column의 속성과 같다.

- #### @ManyToOne은 N : 1 관계 매핑 시 이용하며 여러 속성이 존재하고 연관관계 주인이므로 mappedBy 속성이 없다.

  - #### Optional : false로 설정하면 연관된 엔티티가 항상 있어야 한다. Default 값은 TRUE이다.

  - #### Fetch : 글로벌 패치 전략을 설정한다. @Many가 붙은 어노테이션은 Default 값이 FetchType.EAGER이며 @ONE이 붙은 어노테이션은 Default 값이 FetchType.LAZY이다. 즉시 로딩, 지연 로딩을 설정 가능하다.

  - #### Cascade : 영속성 전이 기능을 사용한다.

  - #### TargetEntity : 연관된 엔티티의 타입 정보를 설정한다. 이 기능은 거의 사용하지 않으며 컬렉션을 사용해도 제네릭으로 타입 정보를 알 수 있다.

- #### @OneToMany는 1 : N 관계 매핑 시 이용하며 여러 속성이 존재한다.

  - #### MappedBy : 연관관계의 주인 필드를 선택하여 매핑한다.

  - #### Fetch : 글로벌 패치 전략을 설정한다. @Many가 붙은 어노테이션은 Default 값이 FetchType.EAGER이며 @ONE이 붙은 어노테이션은 Default 값이 FetchType.LAZY이다. 즉시 로딩, 지연 로딩을 설정 가능하다.

  - #### Cascade : 영속성 전이 기능을 사용한다.

  - #### TargetEntity : 연관된 엔티티의 타입 정보를 설정한다. 이 기능은 거의 사용하지 않으며 컬렉션을 사용해도 제네릭으로 타입 정보를 알 수 있다.



# Advanced Mapping



## Inheritance Mapping



- #### 관계형 데이터베이스는 상속 관계가 없다.

- #### 슈퍼타입 서브타입 관계라는 모델링 기법이 객체 상속과 유사하다.

- #### 관계형 데이터베이스에는 논리, 물리 모델이 존재한다.

- #### 상속 매핑을 할 때 @DiscriminatorColumn(name = "DTYPE")은 구별자를 넣을 컬럼의 이름을 지정한다. Default 값은 DTYPE이다.

- #### @DiscriminatorValue("XXX")는 각 자식 객체에 대한 이름을 부여하는 것이다. 이름을 부여하여 명확하게 구분하는 것이 좋다. Default 값은 엔티티명이다.

- #### @Inheritance(strategy = InheritanceType.XXX)는 상속 전략을 정하는 것이다. Default 값은 SINGLE_TABLE이다.

- #### 객체 입장에서는 어떠한 전략을 사용해도 변하지 않는다.

- #### 어노테이션만 수정하여 상속 매핑 전략을 선택할 수 있다. JPA의 좋은 전략이다.

![관계형논리모델](https://user-images.githubusercontent.com/79822924/152363270-058b6e63-baba-47fc-9308-0df4cfe1de32.png)

![객체상속관계](https://user-images.githubusercontent.com/79822924/152363303-7054564f-92ee-49b0-9485-775191861b0f.png)



## Join Strategy



![조인전략객체](https://user-images.githubusercontent.com/79822924/152363327-33b42ca6-d552-4576-8cb5-56bb8ec10b50.png)

![조인전략디비](https://user-images.githubusercontent.com/79822924/152363349-c6f3b756-4b73-46f5-bdbf-6769871acdf6.png)

- #### 조인 전략은 @Inheritance(strategy = InheritanceType.JOINED)를 사용한다.

- #### 각각 테이블로 변환하여 구성한다.

- #### 조인 전략을 사용하기 전에 데이터베이스에 원하는 형식으로 데이터를 저장하기 위해 h2 데이터베이스 버전을 1.4.200에서 1.4.199로 낮추거나 하이버네이트 버전을 5.3.10에서 5.4.13으로 올려야 한다.

- #### 부모와 자식에 대한 테이블을 데이터를 나눠서 모두 만든 후 조인으로 데이터를 가져온다.

- #### 즉, 위의 그림에서 Album으로 값이 들어오면 name, price는 Item테이블로 들어가며 artist는 Album 테이블로 들어간다.

- #### 조인 전략을 선택하면 이와 같이 Insert 쿼리가 2번 나가게 된다.

- #### 기본키 값은 동일하게 부여되어 사용되어 구분가능하다.

- #### 사실 조인 전략에서는 자식 테이블 모두 쿼리로 조회하면 값 전체가 나오므로 @DiscriminatorColumn 없어도 된다.

- #### 조인 전략에는 장점이 있다.

  - #### 테이블이 정규화된 모델이다.

  - #### 외래키 참조 무결성 제약조건이 활용가능하다. 예를 들어, 주문 테이블에서 Item들을 조회하고 싶을 때 Item 테이블만 접근하면 된다. 또한, 가격 정산 시 Item 테이블만 접근하면 된다.

  - #### 저장공간을 효율적으로 활용할 수 있다.

- #### 조인 전략에는 단점이 있다.

  - #### 조회 시 조인 쿼리를 많이 사용하여 성능 저하가 일어날 수 있다.

  - #### 조회 쿼리가 복잡하게 이루어진다.

  - #### 데이터 저장시 Insert 쿼리가 2번 호출된다.



## Single Table Strategy



![단일테이블전략객체](https://user-images.githubusercontent.com/79822924/152363384-d7f52c4b-8942-4b67-a3cf-e80c494be725.png)

![단일테이블전략디비](https://user-images.githubusercontent.com/79822924/152363397-31b5a7af-07b8-40f3-b8b8-de5b655f0168.png)

- #### 단일 테이블 전략은 @Inheritance(strategy = InheritanceType.SINGLE_TABLE)을 사용한다.

- #### 통합 테이블로 변환하여 구성한다.

- #### 논리 모델을 하나의 테이블로 합친 것이다.

- #### @DiscriminatorColumn이 반드시 필요하다.

- #### DTYPE으로 각 엔티티를 구분한다.

- #### Insert 및 조회 쿼리가 1번 발생해서 성능이 좋고 단순하다.

- #### 단일 테이블 전략에는 장점이 있다.

  - #### 조인이 필요 없으므로 일반적으로 조회 성능이 빠르다.

  - #### 조회 쿼리가 단순하다.

- #### 단일 테이블 전략에는 단점이 있다.

  - #### 자식 엔티티가 매핑한 컬럼은 모두 null을 허용한다. 즉, 데이터 무결성 입장에서 애매하다.

  - #### 단일 테이블에 모든 것을 저장하므로 테이블이 커질 수 있다. 상황에 따라서 조회 성능이 오히려 느려질 수 있다.



## Table Per Class Strategy



![클래스마다테이블객체](https://user-images.githubusercontent.com/79822924/152363427-ab52cb8f-c84a-494a-b334-ae7fff7f8a1c.png)

![클래스마다테이블디비](https://user-images.githubusercontent.com/79822924/152363445-b299af81-da89-4c11-b150-2c2e899621c0.png)

- #### 구현 클래스마다 테이블 전략은 @Inheritance(strategy = InheritanceType.SINGLE_TABLE)을 사용한다.

- #### 서브타입 테이블로 변환하여 구성한다.

- #### 부모는 class가 아닌 abstract class로 생성한다.

- #### 부모 테이블은 생성하지 않고 자식 테이블만 따로 생성한다.

- #### 부모 테이블의 속성이 모두 자식 테이블로 들어간다.

- #### Insert 쿼리 1개 생성된다.

- #### 각 테이블에 대한 코드, 쿼리가 필요하다. 즉, 새로운 타입이 추가될 때 어렵다.

- #### 이 전략은 DBA와 ORM 전문가 둘다 추천하지 않는다.

- #### 구현 클래스마다 테이블 전략은 장점이 있다.

  - #### 서브 타입을 명확하게 구분해서 처리할 때 효과적이다.

  - #### not null 제약조건을 사용 가능하다.

- #### 구현 클래스마다 테이블 전략은 단점이 있다.

  - #### 여러 자식 테이블을 함께 조회할 때 성능이 느리다. 즉, 기본키 값으로 원하는 데이터를 찾을 때 UNION SQL로 모든 자식 테이블을 접근해야 한다.

  - #### 자식 테이블을 통합해서 쿼리하기 어렵다.



## @MappedSuperclass



![MappedSuperclass1](https://user-images.githubusercontent.com/79822924/152363478-f5e2e5b1-b91f-4bf9-ab04-1496ce1589b5.png)

![MappedSuperclass2](https://user-images.githubusercontent.com/79822924/152363494-8252c5a8-51be-4dfa-9fd9-85de204df04c.png)

```java
@MappedSuperclass
public abstract class BaseEntity {

    // 만든 사람, 만든 일시, 수정한 사람, 수정한 일시에 대한 속성이 Member, Team에 공통으로 있다고 할 때 부모 클래스 사용한다.
    @Column(name = "INSERT_MEMBER")
    private String createdBy;
    private LocalDateTime createdDate;
    @Column(name = "UPDATE_MEMBER")
    private String lastModifiedBy;
    private LocalDateTime lastModifiedDate;
}
```



- #### 상속관계 매핑과 관련없다.

- #### 엔티티가 아니므로 테이블과 매핑하지 않는다.

- #### 공통 속성을 묶어서 사용한다.

- #### 매핑 정보만 받는 부모클래스, 슈퍼클래스이다.

- #### 부모 클래스를 상속 받는 자식 클래스에 매핑 정보만 제공한다.

- #### 조회, 검색 불가하다. 즉, 위 코드에서 em.find(BaseEntity) 불가하다.

- #### 직접 생성해서 사용할 일이 없으므로 추상 클래스 권장한다. 즉, abstract class를 사용한다.

- #### 테이블과 관계 없고, 단순히 엔티티가 공통으로 사용하는 매핑정보를 모으는 역할을 한다.

- #### 주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용한다.

- #### 참고로 @Entity 클래스는 엔티티나 @MappedSuperclass로 지정한 클래스만 상속 가능하다.

- #### JPA에서 날짜 데이터에 대한 것은 자동으로 받아올 수 있다. 즉, 코드로 적어주지 않아도 된다. 예를 들어, Admin의 로그인 된 세션 정보를 읽어와서 이름을 넣어줄 수 있다. 즉, JPA의 이벤트 기능으로 해결하거나 어노테이션으로 해결할 수 있다.

- #### 실무에서 처음에 상속으로 객체 지향적으로 설계 후 문제 예상 후 다른 것으로 바꾸는 방식을 취한다.

- #### 위 사진처럼 Item만 단독으로 테이블에 저장할 일 있으면 class 혹은 abstract class를 사용한다.

- #### 상속은 데이터가 적을 때에는 잘 작동하지만 데이터가 억 단위, 파티셔닝 때에는 복잡하다. 테이블을 단순하게 운영해야 한다.



# Managing Proxy & Association



## Proxy



```java
public void printUser(String memberId) {
    Member member = em.find(Member.class, memberId);
    Team team = member.getTeam();
    System.out.println("회원 이름: " + member.getUsername());
}
```

- #### Member 엔티티만 사용할 경우 연관된 Team에 대한 엔티티도 쿼리로 가져오면 낭비가 일어난다.

- #### Find() 메소드는 연관된 엔티티까지 조회하는 기능을 한다.



```java
Member member = em.getReference(Member.class, memberId);
member.getUsername();
```

![proxy](https://user-images.githubusercontent.com/79822924/152363517-e3a070d7-82c3-47d6-9ef3-6ffa58e7b91e.png)

- #### GetReference()는 데이터베이스 조회를 미루는 가짜(프록시) 엔티티 객체를 조회한다.

- #### 프록시는 실제 클래스를 상속 받아서 만들어진다. Hibernate가 지원해준다.

- #### 실제 클래스와 겉 모양이 같으며 사용자 입장에서 진짜와 프록시를 구분하지 않고 사용할 수 있다.

- #### 프록시 객체는 실제 객체에 대한 참조를 보관하여 사용시 참조한다. 즉, 프록시 객체를 호출하면 프록시 객체는 실제 객체의 메소드를 호출한다.

- #### 위 사진과 같이 프록시 객체가 참조가 이루어지지 않았을 경우 우선 영속성 컨텍스트를 확인한다. 영속성 컨텍스트에 원하는 엔티티가 없다면 데이터베이스에 엔티티를 요청한 후 영속성 컨텍스트에 저장하여 참조를 시작한다. 한번 프록시의 target으로 지정되면 같은 정보에 대한 쿼리를 날리지 않는다.

- #### 프록시 객체는 처음 사용할 때 한 번만 초기화하며 프록시 객체는 초기화 해도 실제 엔티티로 바뀌는 것이 아닌 참조만 하여 실제 엔티티에 접근하기만 하는 것이다. 즉, 한번 프록시 객체는 계속 hibernate에서 관리하는 프록시 객체이다.

- #### 프록시 객체는 원본 엔티티를 상속받으므로 타입 체크시 주의해야 한다. 실제값 비교 비즈니스 메소드를 만든 후 프록시 객체와 실제 객체를 비교할 때 ==를 사용하면 서로 class 주소가 다르므로 false가 발생한다. 즉, ==대신 instanceOf()를 사용하여 인스턴스로 비교해야 true가 발생한다.

- #### JPA는 한 트랜잭션에서 실제 엔티티나 프록시 객체가 같은 객체를 이용하면 == 시 무조건 true를 반환한다. 즉, JPA의 기본 메커니즘은 한 트랜잭션에서 같은 객체는 같은 참조를 가진다고 한다.

- #### 프록시 객체를 먼저 생성 후 find()로 동일한 객체를 생성하면 이후에 생성된 객체들은 모두 첫 프록시 객체와 같은 값을 가진다. 즉, 먼저 만들어진 객체의 타입을 따라가게 된다. 영속성 컨텍스트에 찾는 엔티티가 이미 있으면 그 엔티티를 따라간다는 뜻이다.

- #### 아직 target이 정해지지 않은 프록시 객체를 detach()하여 준영속 상태로 만들면 쿼리를 날리지 못하므로 target 지정하는 것이 불가능 해져서 org.hibernate.LazyInitializationException 예외가 발생한다.

- #### PersistenceUnitUtil.isLoaded(Object entity)로 프록시 인스턴스의 초기화 여부를 확인 할 수 있다.

- #### Entity.getClass().getUsername()을 출력했을 때 javasist나 HibernateProxy가 나오면 프록시 객체이다(예전 spring에서는 CGLIB가 프록시 객체를 뜻했다).

- #### JPA 표준에는 강제 초기화가 없다.



## EAGER, LAZY



```java
@Entity
public class Member {

	@Id
	@GeneratedValue
	private Long id;
	
	@Column(name = "USERNAME")
	private String name;
	
	@ManyToOne( fetch = FetchType.LAZY )
	@JoinColumn(name = "TEAM_ID")
	private Team team;
}
```

![Lazy](https://user-images.githubusercontent.com/79822924/152363556-fc2466fc-baf0-4256-a0c9-5dfae240cc3d.png)

- #### FetchType.Lazy는 지연로딩으로 엔티티를 프록시로 조회를 한다는 뜻이다. 즉, 위 코드에서 find() 시 Member 엔티티만 조회하며 Team 엔티티는 프록시로 생성해 놓는 것이다.

- #### 실제 Team을 사용하는 시점에 최기화하여 데이터베이스에서 조회한다. 즉, Team 엔티티는 이미 프록시로 만들어져 있으며 프록시 객체를 사용하여 메소드를 호출하거나 변수를 사용할 때 쿼리가 전송된다.



```java
@Entity
public class Member {

	@Id
	@GeneratedValue
	private Long id;
	
	@Column(name = "USERNAME")
	private String name;
	
	@ManyToOne( fetch = FetchType.EAGER )
	@JoinColumn(name = "TEAM_ID")
	private Team team;
}
```

![EAGER](https://user-images.githubusercontent.com/79822924/152363581-faecf869-733a-4927-a533-974bff473a01.png)

- #### FetchType.EAGER은 즉시로딩으로 연관된 엔티티를 모두 함께 조회한다는 뜻이다. 즉, 위 코드에서 find() 시 Member 엔티티와 Team 엔티티 모두 쿼리가 전송되어 조회된다.



- #### 가급적 지연 로딩만 사용하는 것이 좋다. 즉시로딩으로 하면 DBA에 너무 많은 로직(조인)이 발생할 수 있다.

- #### 즉시로딩은 JQPL에서 N+1 문제를 일으킨다. 즉, 원하는 엔티티를 조회한 후 연관된 엔티티에 대한 객체를 하나하나 쿼리를 전송하여 가져온다. 1은 처음 조회하고자 하는 쿼리이며 N은 연관되어 전송된는 추가 쿼리이다.

- #### 즉, 즉시로딩은 상상하지 못한 쿼리가 나가므로 지연로딩으로 모든 관계를 설정하는 것이 실무에 좋다.

- #### @ManyToOne, @OneToOne는 default로 EAGER가 설정되어 있다.

- #### @OneToMany, @ManyToMany는 default로 LAZY가 설정되어 있다.



## CASCADE



```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)
```

![cascade](https://user-images.githubusercontent.com/79822924/152363599-49c4f0c1-a237-464b-afcc-3ecc00550218.png)

- #### Cascade는 특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶을 때 사용한다.

- #### 즉, cascade로 된 엔티티 객체 내부에 연관된 다른 엔티티 객체 모두 영속성 컨텍스트로 저장할 수 있다. Persist() 시 모두 한꺼번에 영속성 컨텍스트로 저장할 수 있다.

- #### Cascade(영속성 전이)는 연관관계 매핑과는 관련이 없으며 한번에 영속화하는 편리함만 제공할 뿐이다.

- #### Cascade는 하나의 부모가 자식들을 관리할 때 의미가 있다. 예를 들어, 첨부 파일은 하나의 게시물에서만 관리하므로 cascade에 어울린다. 하지만 파일을 여러 엔티티에서 관리하면 cascade를 사용하면 안됀다.

- #### Cascade에는 여러 종류가 있다.

  - #### ALL : 모두 적용하는 것으로 lifecycle 다 맞춘 공동체 연관일 때 사용한다.

  - #### PERSIST : 저장할 때에만 적용한다.

  - #### REMOVE : 삭제할 때에만 적용한다.

  - #### MERGE : 병합할 때에만 적용한다.

  - #### REFRESH : REFRESH할 때에만 적용한다.

  - #### DETACH : DETACH할 때에만 적용한다.



## Orphan



```java
@OneToMany(mappedBy = "parent", orphanRemoval = true)
```

- #### 고아 객체는 부모에서 자식 객체를 관리한다는 뜻이다.

- #### Cascade와 차이점은 부모에서 자식만 삭제하는 것이 가능하다는 것이다.

- #### 부모가 지워지면 자식이 고아가 되므로 자식도 모두 삭제한다.



```java
Parent parent1 = em.find(Parent.class, id);
parent1.getChildren().remove(0);
```

- #### 부모 list에서 child를 삭제하면 그 child는 데이터베이스에서도 삭제된다. 즉, 참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 판단하여 삭제하는 것이다.

- #### 고아 객체 선언은 참조하는 곳이 하나일 때 사용해야 한다.

- #### 고아 객체 선언은 엔티티가 개인 소유할 때 사용해야 한다.

- #### @OneToOne, @OneToMany일 경우에만 사용 가능하다.

- #### 부모가 사라지면 자식도 사라지는 것은 CascadeType.REMOVE와 비슷하게 작동한다.



## Cascade + Orphan



```java
@OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
```

- #### 부모 엔티티의 경우 스스로 생명주기를 관리하여 persist()로 영속화 remove로 제거할 수 있다. 즉, lifecycle을 엔티티 매니저를 통해 관리하는 것이다.

- #### 자식 엔티티의 경우 cascade, orphan 옵션이 모두 활성화 되면 부모 엔티티를 통해서 lifecycle을 관리할 수 있다.

- #### 도메인 주도 설계(DDD)의 Aggregate Root 개념을 구현할 때 유용하다. Repository는 Aggregate Root로 컨택하고 나머지 비즈니스는 repository로 만들지 않는다. 즉, Aggregate Root를 통해서 모든 lifecycle을 관리하는 것이다.



# Value Type



## Basic Value Type

- #### 값 타입은 3가지로 분류할 수 있다.

  - #### 기본값 타입 : 자바 기본 타입(int, double, ...), 래퍼 클래스(Integer, Long, ...), String 등이 있다.

  - #### 임베디드 타입 : embedded type, 복합 값 타입으로 좌표 등을 표현할 때 유용하다.

  - #### 컬렉션 값 타입 : collection value type으로 collection에 기본값 타입을 넣는다.

- #### 기본값 타입은 생명주기를 엔티티에 의존하며 공유하면 side effect가 많아서 공유하면 안됀다.

- #### 자바의 기본 타입은 절대 공유하면 안됀다.

- ####  Int, double 같은 기본 타입은 값 타입시 독립이므로 안전하며 절대 공유하면 안됀다.

- #### 기본 타입은 항상 깊은 복사를 한다.

- #### Integer 같은 래퍼 클래스나 String 같은 특수한 클래스는 공유 가능한 객체이지만 변경하면 안됀다. String 클래스는 깊은 복사가 되는 것처럼 보이지만 새로운 메모리를 할당하여 새 객체를 생성한다. 하지만 참조를 하는 것이지만 변경 자체가 일어날 수 없으므로 안전하다.

- #### 참고로 클래스는 모두 얕은 복사가 일어난다.



## Embedded Type



- #### 임베디드 타입은 새로운 값 타입을 직접 정의할 수 있다.

- #### 내장 타입이며 엔티티가 아닌 값 타입이다.

- #### 주로 추적 불가한 기본 값 타입을 모아서 만들어서 복합 값 타입이라고도 부른다.



![embedded전](https://user-images.githubusercontent.com/79822924/152363633-124ae9d4-d174-4e81-9129-d7a7a8a18ca0.png)

![embedded후](https://user-images.githubusercontent.com/79822924/152363648-48467239-df29-4a33-a636-5f7104271133.png)

- #### 임베디드 타입은 값 타입을 묶어서 엔티티에 전달할 수 있다.

- #### @Embeddable은 값 타입을 정의하는 곳에 표시하여 사용할 수 있다.

- #### @Embedded는 값 타입을 사용하는 곳에 표시하여 사용할 수 있다.

- #### @Embeddable이 붙은 값 타입 객체는 기본 생성자가 필수이다.

- #### 임베디드 타입에는 장점들이 있다.

  - #### 시스템 전체에서 재사용이 가능하다.

  - #### 클래스 내부에서 응집도가 높다.

  - #### 해당 값 타입만 사용하는 의미있는 메소드를 만들 수 있다.

  - #### 임베디드 타입을 포함한 모든 값 타입은 값 타입을 소유한 엔티티에 생명주기를 의존한다. 즉, 엔티티가 삭제되면 같이 삭제되고 엔티티가 생성될 때 값이 들어와서 생성된다.

- #### 값 타입은 공유 시 데이터를 수정하면 공유 중인 다른 엔티티에도 영향을 미친다. 서로 영향을 미치면 안돼므로 복사본을 만든 후 각 엔티티에 따로 설정해야 한다.

- #### 임베디드 타입은 엔티티의 값일 뿐이므로 사용하기 전과 후에 매핑하는 테이블은 동일하다.

- #### 임베디드 타입으로 객체와 테이블을 아주 세밀하게(fine-grained) 매핑하는 것이 가능하다. 예를 들어, 근무 시작, 근무 끝을 근무기간으로 만들 수 있다.

- #### 임베디드 타입으로 용어, 코드를 공통화하여 잘 설계한 ORM 어플리케이션을 만들 수 있다. 잘 설계한 ORM 어플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많다.

- #### 임베디드 타입은 임베디드 타입을 값 타입으로 저장할 수 있다.

- #### 임베디드 타입의 값이 null이면 매핑한 컬럼 값은 모두 null이다.

- #### 임베디드는 테이블을 생성하지 않으며 비즈니스 메소드를 임베디드 클래스 내에 생성할 수 있다.

- #### UML에서 임베디드 클래스처럼 따로 생성되어 있는 것을 StereoType 이라고 부른다.



```java
    @Embedded
    @AttributeOverrides({
            @AttributeOverride(name = "city",
            column = @Column(name = "WORK_CITY")),
            @AttributeOverride(name = "street",
            column = @Column(name = "WORK_STREET")),
            @AttributeOverride(name = "zipcode",
            column = @Column(name = "WORK_ZIPCODE"))
    })
    private Address workAddress;
```

- #### 한 엔티티에서 같은 값 타입을 중복하여 사용할 때 컬럼명이 중복되어 오류가 난다.

- #### @AttributeOverrides, @AttributeOverride를 사용하여 컬럼명 속성을 재정의하여 중복하여 값 타입을 사용할 수 있다.



## Immutable Object



![불변객체 오류](https://user-images.githubusercontent.com/79822924/152363691-1ffa0ab7-d846-4440-8c5b-21de690dd0c2.png)

- #### 같은 주소를 참조할 때 값을 바꾸면 양쪽 엔티티에 적용되는 부작용이 발생한다.

- #### 이러한 side effect 버그는 잡아내기 힘들다.

- #### 만약 공유가 목적이었다면 값 타입이 아닌 엔티티로 생성하여 사용해야 한다.



```java
Address copyAddress = new Address(address.getCity(), address.getStreet(), address.getZipcode());
Member member2 = new Member();
member2.setHomeAddress(copyAddress);
```

- #### 값 타입을 따로 저장하여 사용하고 싶으면 복사본을 만든 후 주입해야 한다.

- #### 임베디드 타입은 객체 타입이므로 복사 시 참조 값을 공유하게 된다.

- #### 참조 공유를 방지하기 위해 값 타입은 불변 객체로 설계해야 한다.

- #### 생성 시점 이후 절대 값을 변경할 수 없는 객체를 만들기 위해 생성자로만 값을 설정하고 수정자를 생성하지 않는다.

- #### Integer, String은 자바가 제공하는 대표적인 불변 객체이다.



```java
Address newAddress = new Address("NewCity", address.getStreet(), address.getZipcode());
member1.setHomeAddress(newAddress);
```

- #### 불변 객체의 값을 바꾸고 싶다면 통으로 새로운 객체를 생성 후 저장해야 한다.



```java
int a = 10;
int b = 10;
// a == b true

Address c = new Address("서울시");
Address d = new Address("서울시");
// c == d false
// c.equals(d) true // equals와 hashcode를 override 해야 한다
```

- #### 값 타임은 인스턴스가 달라도 그 안에 값이 같으면 같은 것으로 봐야 한다.

- #### 동일성(identity) 비교란 인스턴스의 참조 값을 비교하는 것으로 ==를 사용한다.

- #### 동등성(equivalence) 비교란 인스턴스의 값을 비교하는 것으로 equals()를 사용한다.

- #### 값 타입은 동등성 비교를 이용하여 비교한다. Equals() 메소드를 적절하게 재정의 하여 사용한다. Equals()는 default로 ==비교를 하므로 override하여 기준을 바꿔야 한다. Equals()를 구현하면 hashcode도 그것에 맞게 구현해야 한다. Hashcode를 구현하면 hashmap, collection을 효율적으로 이용 가능하다.



## Value Type Collection



![값타입컬렉션1](https://user-images.githubusercontent.com/79822924/152363713-26e38d0b-d0ea-4841-85e2-4a739b0ba486.png)

![값타입컬렉션2](https://user-images.githubusercontent.com/79822924/152363725-209b76ef-312b-456c-b6cf-e856552180fd.png)

- #### 값 타입 컬렉션은 값 타입을 컬렉션에 담아서 사용하는 것이다.

- #### 값 타입으로 묶어서 모든 속성을 기본키로 활용할 수 있다.

- #### 값 타입 컬렉션은 값 타입을 하나 이상 저장할 때 사용한다.

- #### 데이터베이스는 컬렉션을 같은 테이블에 저장할 수 없으므로 컬렉션을 저장하기 위한 별도의 테이블을 생성한다. 별도의 테이블을 만든 후 소속된 엔티티에 1 : N 연관관계를 매핑한다.



```java
	@ElementCollection
    @CollectionTable(name = "FAVORITE_FOOD", joinColumns =
    @JoinColumn(name = "MEMBER_ID")
    )
    @Column(name = "FOOD_NAME") // 값이 하나이고 정의한 것이 아니므로 컬럼명을 지정한다.
    private Set<String> favoriteFoods = new HashSet<>();

    // Column명을 embedded 타입 그냥 사용하면 된다.
//    @ElementCollection
//    @CollectionTable(name = "ADDRESS", joinColumns =
//    @JoinColumn(name = "MEMBER_ID")
//    )
//    private List<Address> addressHistory = new ArrayList<>();

//     값 타입 컬렉션 대신 1 : N 관계 엔티티를 사용한다.
    @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
    @JoinColumn(name = "MEMBER_ID")
    private List<AddressEntity> addressHistory = new ArrayList<>();
```

- #### @ElementCollection은 값 타입 컬렉션의 매핑을 알리는 어노테이션이다.

- #### @CollectionTable은 값 타입 컬렉션을 위한 테이블을 생성하는 것이다. @JoinColumn을 사용하여 소속된 엔티티의 기본키를 외래키로 매핑할 수 있다.

- #### 값 타입 테이블은 컬렉션 수만큼 레코드가 저장되어 소속된 엔티티의 기본키가 연속하여 사용되고 리스트의 길이만큼 레코드가 저장된다.

- #### 값 타입 컬렉션은 소속된 엔티티가 persist()하면 자동으로 함께 영속성으로 관리된다. 즉, 소속된 엔티티의 생명주기에 의존하는 것이다.

- #### 값 타입 컬렉션은 모두 지연 로딩을 지원한다.

- #### 값 타입 컬렉션은 영속성 전이(Cascade)와 고아 객체 제거 기능을 필수로 가진다.



```java
findMember.getAddressHistory().remove(new Address("old1", "street", "10000"));
findMember.getAddressHistory().add(new Address("newCity1", "street", "10000"));
```

- #### 클래스로 된 값 타입 컬렉션 수정 시 remove 할 때 객체가 equals()로 비교하기 때문에 위와 같은 로직으로 수행해야 한다. 물론 객체 엔티티에 equals(), hashcode를 override 해야 한다.

- #### 값 타입은 엔티티와 다르게 식별자 개념이 없어서 값 변경 시 추적이 불가하다.

- #### 값 타입 컬렉션에 변경 사항이 발생하면 주인 엔티티와 연관된 모든 데이터를 삭제하고 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장한다. 즉, 초기화 후 변경된 내용을 다시 저장하는 것으로 이런 로직은 사용하면 안됀다.

- #### 값 타입 컬렉션을 매핑하는 테이블은 각 컬럼에 기본키를 할 속성이 없으므로 모든 값을 묶어서 기본키로 사용해야 한다. 즉, null 입력하면 안돼며 중복 저장도 허용해서는 안됀다.



```java
@Entity
@Table(name = "ADDRESS")
public class AddressEntity {

    public AddressEntity() {
    }

    public AddressEntity(String city, String street, String zipcode)
    {
        this.address = new Address(city, street, zipcode);
    }

    public AddressEntity(Address address){
        this.address = address;
    }

    @Id
    @GeneratedValue
    private Long id;

    private Address address;
}
```



- #### 대안으로 실무에서는 상황에 따라 값 타입 컬렉션 대신 1 : N 관계를 고려하여 사용한다. 즉, 엔티티로 래핑하여 값 타입으로 승급하는 것이다. 고유의 기본키를 사용한다.

- #### 1 : N 관계를 위한 엔티티를 만들고 엔티티에 값 타입을 적용하는 것이다.

- #### 엔티티를 매핑하여 영속성 전이(Cascade)와 고아객체 제거를 사용하여 값 타입 컬렉션처럼 사용할 수 있다.

- #### 값 타입 컬렉션은 매우 단순할 때 이용해야 한다. 예를 들어, select box 에 치킨, 피자가 있고 둘 다 고를 수 있을 때 사용한다.

- #### 식별자가 필요하고 지속해서 값을 추적하고 변경해야 한다면 그것은 값 타입이 아닌 엔티티로 관리해야 한다.





# Additional Contents



- #### 연관관계 매핑 시 컬럼명을 @Column(name = "PARENT")로 설정하지 않아도 @JoinColumn(name = "parent_id")로 자동으로 매핑 가능하다.

- #### Equals(), hashcode 만들 때 getters를 허용해야 한다. 프록시로 equals()를 실행할 때 getter을 통해 쿼리를 전송하고 데이터를 받아온다.



Image 출처 : [김영한 ORM 표준 JPA](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)



