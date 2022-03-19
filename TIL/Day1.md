### 01. JPA에 대해 알아보기에 앞서...
---
#### SQL을 직접 다룰때 발생하는 문제점
+ CRUD 기능을 만들경우 각각의 기능마다 SQL문을 만들고 JDBC API를 사용해서 SQL을 실행해야 한다.
+ 즉, 개발하려는 애플리케이션에서 사용하는 데이터베이스 테이블이 100개라면 무수히 많은 SQL을 작성해야 하고 JDBC API를 사용하여 변환해주는 작업을 100번 수행해야 한다.
+ 또한 필드를 하나 추가할 경우 DAO의 CRUD코드와 SQL 대부분을 변경해야 한다.

#### JPA를 통한 문제해결
+ JPA를 사용하면 객체를 데이터베이스에 저장하고 관린할 때, 개발자가 직접 SQL을 작성하는 것이 아니라 JPA가 제공하는 API를 사용하면 된다.
+ 그러면 JPA가 개발자 대신 SQL을 생성해서 데이터베이스에 전달한다.
  + `persist() 메소드`
    + 예) `jpa.persist(member);`
    + 객체를 데이터베이스에 저장
    + JPA가 객체와 매핑정보를 보고 적절한 `INSERT`문을 생성해서 데이터베이스에 전달
  + `find()메소드`
    ```java
    String memberId = "helloId";
    Member member = jpa.find(Member.class, memberId);
    ```
    + 객체 하나를 데이터베이스에서 조회
    +  JPA가 객체와 매핑정보를 보고 적절한 `SELECT`문을 생성해서 데이터베이스에 전달
    +  그 결과로 `Member`객체를 생성해서 반환
  + JPA는 별도의 수정 메소드를 제공하지 않는다.
    ```java
    Member member = jpa.find(Member.class, memberId);
    member.setName("이름변경") // 수정
    ```
    + 대신에 객체를 조회해서 값을 변경만 하면 트랜잭션을 커밋할 때 데이터베이스에 적절한 `UPDATE`문이 전달된다.
  + 연관된 객체 조회
    + JPA는 연관된 객체를 사용하는 시점에 적절한 `SELECT`문을 실행한다.

+ 객체는 추상화, 상속, 다형성이라는 개념이 있는데 관계형 데이터베이스는 데이터 중심으로 구조화되어 있어서 객체가 가지고 있는 개념은 없다.
+ 이러한 문제를 `패러다임 불일치` 라고 부른다. 이러한 문제는 JPA에 객체를 저장하므로써 해결된다.
  ```java
  abstract class Item{
    Long id;
    String name;
    int price;
  }

  class Album extends Item{
    String artist;
  }
  ```
  + Album 객체를 저장하려면 이 객체를 나눠서 SQL문을 두개 만들어야 한다.
  ```
  INSERT INTO ITEM...
  INSERT INTO ALBUM...
  ```
  + 이렇게 하면 작성해야 하는 코드기 많아진다.
  + 그러나 JPA는 한줄이면 된다.
  + JPA를 사용해서 `Item`을 상속한 `Album`객체를 저장해보자.(persist()메소드를 사용) -> `jpa.persist(album);`
  + 그러면 JPA는 `INSERT INTO ITEM...`와 `INSERT INTO ALBUM...`이렇게 두 객체를 각각의 테이블에 나누어 저장한다.
  + 조회의 경우 두 테이블을 조인해서  필요한 데이터를 조회하고 그 결과를 반환한다.
  ```java
  String albumId = "id100";
  Album album = jpa.find(Album.class, albumId);

  //JPA 수행
  SELECT I.*, A.* FROM ITEM I JOIN ALBUM ON I.ITEM_ID = A.ITEM_ID
  ```
+ JPA는 연관관계와 관련된 불일치 문제를 해결해준다.
  ```java
  member.setTeam(team); // 회원과 팀 연관관계 설정 
  jpa.persist(member); // 회원과 연관관계가 함께 저장
  ```
  + 회원과 팀의 관계를 설정하고 회원 객체를 저장한다.
  + 그러면 JPA는 team의 참조를 외래 키로 변환해서 적절한 INSERT문을 데이터베이스에 전달한다.
  + 객체를 조회할 때 외래키를 참조하는 일도 JPA가 처리해준다.
  ```java
  Member member = jpa.find(Member.class, memberId)'
  Team team = member.getTeam();
  ```
+ JPA는 객체 그래프를 마음껏 탐색할 수 있다.
  + JPA는 객체를 사용하는 시점에 적절한 SELECT문을 실행한다. 그래서 연관된 객체를 신뢰하고 언제든지 조회할 수 있다.
  + 이 기능은 실제 객체를 사용하는 시점까지 데이터베이스 조회를 미룬다고 해서 `지연 로딩`이라고 한다.
  + 지연로딩 사용 예제
  ```java
  //처음 조회 시점에 SELECT MEMBER SQL문 실행
  Member member = jpa.find(Member.class, memberId);

  Order order = member.getOrder();
  order.getOrderDate(); // Order를 사용하는 시점에 SELECT ORDER SQL문 실행
  ```
  + `order.getOrderDate();`같이 실제 Order객체를 사용하는 시점에 JPA는 데이터베이스에서 ORDER 테이블을 조회한다.
  + JPA는 연관된 객체를 함께 조회할지 아니면 실제 사용되는 시점에 지연로딩으로 조회할지 설정할 수 있다.
  + 만약 Member와 Order를 함꼐 조회한다고 설정하면 JPA는 Member를 조회할 때 연관된 Order도 함께 조회한다.
  ```
  //연관된 Order가 함꼐 조회될 때 실행되는 SQL문
  SELECT M.*, O.* FROM MEMBER M JOIN ORDER O ON M.MEMBER_ID = O.MEMBER_ID
  ```

🎁 즉, JPA는 패러다임의 불일치 문제를 해결해주고 정교한 객체 모델링을 유지하게 도와준다.😉 🎁









