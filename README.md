# JDBC
## 데이터베이스 연결
![](https://velog.velcdn.com/images/pipiolo/post/0baa42f9-d6fe-40c8-8b0d-aa363448d6f6/image.png)

* 서버와 데이터베이스는 다음과 같은 방법으로 연결한다.
  * `TCP/IP` 를 사용해서 커넥션을 연결한다.
  * 커넥션을 통해 SQL 을 DB 에 전달한다.
  * DB 는 전달된 SQL 을 수행하고 응답 결과를 서버에 보내준다.
* 각 데이터베이스 마다 커넥션을 연결하는 방법, SQL 전달하는 방법 등 방법이 모두 다르다.
  * 데이터베이스 변경은 많은 비용을 요구한다.
  
## JDBC 이해
![](https://velog.velcdn.com/images/pipiolo/post/c2f1b7e4-b572-4dc7-a50f-ebb6c887b2cf/image.png)

* 각 데이터베이스마다 사용법이 다르다. 
* 자바는 데이터베이스에 접속할 수 있는 `JDBC(Java Database Connectivity)` 인터페이스를 제공한다.
  * 각 데이터베이스 회사들이 JDBC 인터페이스 구현체를 제공하는데, 이를 JDBC 드라이버라 한다.
* JDBC 인터페이스에만 의존하면, 데이터베이스를 변경해도 DB 접속하는 코드는 변경할 필요 없다.

### JDBC 데이터베이스 연결
![](https://velog.velcdn.com/images/pipiolo/post/256cb057-b498-474d-91e3-c2f5b0d2406e/image.png)

* JDBC 가 제공하는 `DriverManager.getConnection(URL, USERNAME, PASSWORD)`을 사용한다.
* `DriverManager` 는 라이브러리에 등록된 DB 드라이버를 인식한다.
  * `URL` : `jdbc:h2:tcp://localhost/~/test`
  * 각각의 DB 드라이버는 URL 정보를 통해 처리할 수 있는 요청인지 확인한다.
    * `jdbc:h2` → H2 데이터베이스
  * `USERNAME`, `PASSWORD` 등 접속에 필요한 추가 정보를 입력한다.
* 커넥션 구현체를 반환한다.

> **표준화의 한계**
JDBC 의 등장으로 많은 것들이 편리해졌지만 각각의 데이터베이스마다 SQL, 데이터 타입 등 일부 사용법이 다르다. JDBC 를 통해 커넥션을 얻는 방법은 변경하지 않아도 되지만, SQL 은 해당 데이터베이스에 맞게 변경해야 한다. 이는 **JPA**(`Java Persistence API`)를 통해 많은 부분을 해결할 수 있다.

## 데이터 접근 기술
* SQL Mapper
  * JDBC 를 편리하게 사용하도록 도와준다.
  * 개발자가 SQL 을 직접 작성해야 한다.
  * 대표 기술 : `JdbcTemplate`, `MyBatis`
* ORM
  * 객체를 관계형 데이터베이스 테이블과 매핑해주는 기술이다.
  * 개발자는 직접 SQL 을 작성하지 않고, ORM 기술이 동적으로 SQL 을 만들어준다.
  * 각각의 데이터베이스마다 다른 SQL 문제도 해결해준다.
  * 대표 기술 : `JPA`, `Hibernate`
    * JPA는 자바 진영의 ORM 표준 인터페이스고 하이버네이트는 구현체이다.
* 공통점
  * 내부에는 모두 JDBC 를 사용한다.

---
