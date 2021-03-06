### JPA란?
---
#### JPA란 무엇인가
+ JPA는 자바 ORM 기술 표준으로서 애플리케이션과 JDBC 사이에서 동작한다.
+ 하이버네이트를 기반의 ORM 기술 표준으로 인터페이스를 모아둔 것이다.
+ 장점
  + 특정 구현 기술에 대한 의존도를 줄일 수 있고 다른 구현 기술로 손쉽게 이동할 수 있다.

#### ORM이란?
+ 객체와 관계형 데이터베이스를 매핑한다는 뜻이다.
+ `ORM 프레임워크`는 객체와 테이블을 매핑해서 패러다임의 불일치 문제를 개발자 대신 해결해 준다.
+ 예를들어, 객체를 데이터베이스에 저장할 때 `INSERT문`을 직접 작성하는 것이 아니라 `ORM 프레임워크`가 적절한 `INSERT문`을 생성해서 데이터베이스에 객체를 저장해준다.
+ 자바 진영에도 다양한 ORM 프레임워크들이 있는데 그중에 `하이버네이트 프레임워크`가 가장 많이 사용된다.
+ 하이버네이트는 대부분의 패러다임 불일치 문제를 해결해 준다.

#### 왜 JPA를 사용해야 할까?🧐
+ 생산성 높음
  + 자바 컬렉션에 객체를 저장하드이 JPA에게 저장할 객체를 전달하면 된다.
  ```java
  jpa.persist(member); //저장
  Member member = jpa.find(memberId); //조회
  ```
  + 그리고 개발자가 직접 CRUD용 SQL문을 작성하지 않아도 된다.
+ 유지보수 편리
  + 필드를 추가하거나 삭제 등을 할 때 관련된 모든 SQL을 변경해줄 필요가 없다.(JPA가 대신처리)

+ 패러다임의 불일치 해결
  + 상속, 연관관계, 객체 그래프 탐색 등 전반적인 불일치 문제를 해결해준다.

+ 성능
  + 애플리케이션과 데이터베이스 사이에서 다양한 성능 최적화를 제공한다.
  ```java
  // 같은 트랜젝션 안에서 같은 회원을 두 번 조회하는 코드의 일부분
  String memberId = "helloId";
  Member member1 = jpa.find(memberId);
  Member member2 = jpa.find(memberId);
  ```
  + 기존에는 데이터베이스와 두번 통신을 했겠지만 JPA를 사용하면 SQL문을 한 번만 데이터베이스에 전달하고 두 번째는 조회한 회원 객체를 재사용한다.
+ 데이터 접근 추상화와 벤더 독립성
  + 관계형 데이터베이스는 같은 기능도 벤더마다 사용법이 다라서 한가지 기술에 종속되고 다른 데이터베이스로 변경하기 어렵다.
  + 그러나 JPA는 접근 계층을 제공해서 애플리케이션이 특정 데이터베이스 기술에 종속되지 않도록 한다.
  + 데이터베이스를 변경하면 JPA에게 다른 데이터베이스를 사용한다고 알려주기만 하면 된다.

#### 객체매핑
+ 매핑 정보를 표시하는 어노테이션
  + @Entity : 이 클래스를 테이블과 매핑한다고 JPA에게 알려준다.
  + @Table : 엔티티 클래스에 매핑할 테이블 정보를 알려준다. `name` 속성을 사용해서 클래스 엔티티(Member)를 해당 테이블명(MEMBER)에 매핑한다.  
  + @Id : 엔티티 클래스의 필드를 테이블의 기본 키에 매핑한다.
  + @Column : 필드를 컬럼에 매핑한다.(name속성 사용)
  + 매핑 정보가 없는 필드 : 매핑 어노테이션을 생략하면 필드명을 사용해서 컬럼명으로 매핑한다.


#### 데이터베이스 방언
+ 데이터 타입: 가변 문자 타입으로 `MySQL`은 `VARCHAR`, `오라클`은 `VARCHAR2`를 사용한다.
+ 문자열 자르는 함수 : 표준은 `SUBSTRING()` 이지만 `오라클`은 `SUBSTR()`을 사용한다.
+ 페이징 처리: `MySQL`은 `LIMIT`을 사용하지만 `오라클`은 `ROWNUM`을 사용한다.










