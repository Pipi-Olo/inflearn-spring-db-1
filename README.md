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

# 커넷션 풀과 데이터 소스
## 커넥션 풀 이해
### 데이터베이스 커넥션 획득
![](https://velog.velcdn.com/images/pipiolo/post/cfb173ab-7b9d-46c4-a9fa-1e0a79f12cce/image.png)

* 데이터베이스에 연결할 때마다 커넥션을 새로 생성하는 것은 과정도 복잡하고 시간이 많이 소모된다.

### 커넥션 풀
![](https://velog.velcdn.com/images/pipiolo/post/8cd99f63-6c51-40df-90c2-cca6b418d790/image.png)

* 커넥션을 미리 생성해두고 사용하는 **커넥션 풀**을 통해 해결할 수 있다.
  * 애플리케이션이 시작하는 시점에 필요한 만큼 미리 커넥션을 생성해서 풀에 보관한다. 
  * 기본 값은 10개이다.
* 커넥션은 `TCP / IP` 로 DB 와 연결되어 있기 때문에 즉시 SQL 을 DB 에 전달할 수 있다.
* 애플리케이션은 커넥션 풀을 통해 이미 생성된 커넥션을 객체 참조로 가져다 쓴다. 
  * 커넥션을 모두 사용하고 나면, 커넥션을 종료하는 것이 아니라 커넥션을 그대로 풀에 반환한다.
* 최대 커넥션 수가 제한되어 있어, DB 연결이 무한정 늘어나는 것을 막아주는 DB 보호 기능 효과도 있다.
* 스프링 부트 2.0 부터는 `HikariCP` 가 기본 커넥션 풀로 작동한다.

## 데이터 소스
![](https://velog.velcdn.com/images/pipiolo/post/f4f1256a-a68d-4665-a107-6a289c6aa29f/image.png)

* 커넥션 획득 방법을 추상화한 인터페이스를 **`DataSource`** 라 한다.
  * `DataSource` 의 핵심 기능은 커넥션 조회이다.
* 커넥션 획득은 `DataSource` 인터페이스에 의존하면 된다.

> **참고**
> `DriverManager` 는 `DataSource` 인터페이스를 사용하지 않는다. 스프링은 `DriverManagerDataSource` 라는 `DataSource` 인터페이스를 구현한 클래스를 제공한다.

---

# 트랜잭션 이해
* 트랜잭션은 하나의 거래를 안전하게 처리하도록 보장해준다.
* 모든 작업을 성공해서 데이터베이스에 정상 반영하는 것을 `commit` 이라 한다.
* 작업 중 하나라도 실패해서 되돌리는 것을 `rollback` 이라 한다.

## 트랜잭션 ACID

* 원자성(`Atomicity`) 
  👉 트랙잭션 내에서 실행한 작업들은 마치 하나인 것처럼 모두 성공하거나 모두 실패해야 한다.
* 일관성(`Consistency`)
  👉 모든 트랜잭션은 일관성 있는 데이터베이스 상태를 유지해야 한다.
* 격리성(`Isolation`)
  👉 동시에 실행되는 트랜잭션들이 서로에게 영향을 미치지 않아야 한다. 
  * 격리성은 동시성과 관련된 성능 이슈로 인해 격리 수준을 선택할 수 있다.
    * 커밋되지 않은 읽기(READ UNCOMMITED)
    * 커밋된 읽기(READ COMMITTED) : 가장 많이 사용한다.
    * 반복 가능한 읽기(REPEATABLE READ)
    * 직렬화(SERIALIZABLE)
* 지속성(`Durability`)
  👉 트랜잭션을 성공적으로 끝내면 그 결과가 항상 기록되어야 한다. 
  * 문제가 발생해도 데이터베이스 로그를 통해 성공한 트랙잭션을 복구해야 한다.

## 데이터베이스 구조와 세션 이해
![](https://velog.velcdn.com/images/pipiolo/post/e19370a6-4ad7-492e-a101-561265966605/image.png)

* 서버는 데이터베이스에 연결을 요청하고 커넥션을 얻는다.
* 데이터베이스는 내부에 세션을 만든다.
  * 커넥션을 통한 모든 요청은 세션을 통해서 실행하게 된다.
  * 세션이 SQL 및 트랜잭션을 시작한다.
* 커넥션 : 세션 = 1 : 1 항상 만족한다.
  * 커넥션 풀이 10개의 커넥션을 생성하면, 데이터베이스는 10개의 세션을 만든다.

### Commit vs Rollback

* 데이터를 변경하고 그 결과를 반영하려면 `commit` 을 호출한다.
* 결과를 반영하고 싶지 않으면 `rollback` 을 호출한다.
* `commit` 을 호출하기 전까지는 데이터를 임시로 저장하는 것이다.
  * `commit` 을 하지 않아도, 해당 세션은 변경된 데이터가 보인다.
  * `commit` 을 하기 전까지, 다른 세션은 변경된 데이터가 보이지 않는다.

### 자동커밋 vs 수동커밋

* 자동 커밋
  * 트랜잭션 기본 동작이다.
  * 각각의 쿼리 실행 직후에 자동으로 커밋을 호출한다.
  * 트랜잭션 기능을 제대로 사용할 수 없다.
    * 트랜잭션은 정상 동작하나 1개의 쿼리 단위로 적용되기 때문에, 기능이 제한적이다.
* 수동 커밋
  * `set autocommit false`
  * 수동 커밋 모드로 설정하는 것을 **트랜잭션이 시작**한다고 표현한다.
  * 수동 커밋 설정을 하면 꼭 `commit` 혹은 `rollback` 을 해줘야 한다.
* 수동 커밋 모드나 자동 커밋 모드는 한 번 설정하면 변경하기 전까지 해당 세션에서는 계속 유지된다.
  * 커넥션 풀을 사용한다면, 반납 하기 전 다시 자동 커밋모드로 되돌리자.

## 데이터베이스 락(Lock)

* 여러 세션에서 동시에 같은 데이터를 수정하게 되면 문제가 발생한다. 
  * 트랜잭션의 원자성이 깨진다.
* 한 세션이 데이터를 수정하는 동안에는 다른 세션에서 해당 데이터를 수정할 수 없게 막아야 한다.

### 변경과 락(Lock)
![](https://velog.velcdn.com/images/pipiolo/post/ac070f07-accc-433b-9348-9ad4975128b4/image.png)

* 세션 1이 트랜잭션을 시작하면, 해당 로우(데이터)의 락을 먼저 획득해야 한다.
* 세션 2도 같은 데이터를 변경하려고 시도한다. 
  * 락이 없으므로 락이 돌아올 때 까지 기다린다.
  * 무한정 대기하는 것은 아니다. 일정 시간을 넘어가면 락 타임아웃 오류가 발생한다.
* 세션 1이 커밋을 수행한다. 트랜잭션이 종료되었으므로 락을 반납한다.
* 세션 2는 락을 획득한다. 데이터를 변경한다.
* 세션 2는 커밋을 하고 트랜잭션을 종료한다. 락을 반납한다.

### 조회와 락(Lock)

* 일반적으로 조회는 락을 사용하지 않는다.
  * 다른 세션이 락을 획득 하고 데이터를 변경하고 있어도, 데이터 조회는 할 수 있다.
    * 조회한 데이터는 아직 다른 세션에서 커밋을 하지 않았기 때문에 다른 세션이 변경하기 이전의 데이터이다.
* 데이터를 조회할 때도 락을 획득하고 싶을 때, `select for update` 구문을 사용한다.
  * 다른 세션에서는 데이터를 변경할 수 없다.
  * 커밋하거나 롤백하면 락을 반납한다.
  * 계좌 이체 같은 해당 데이터를 다른 곳에서 변경하지 못하도록 강제로 막아야 할 떄 사용한다.

## 비지니스 로직과 트랜잭션
![](https://velog.velcdn.com/images/pipiolo/post/54f6e0dd-e9c9-43ef-808b-5a3f4e795ce5/image.png)

* 트랜잭션은 비지니스 로직이 있는 서비스 계층에서 시작해야 한다.
* 서비스 계층의 커넥션과 리포지토리의 커넥션을 같게 유지해야 한다.
  * 트랜잭션을 사용하는 동안 같은 커넥션을 유지해야 한다.

---

# 트랜잭션
## 애플리케이션 로직
![](https://velog.velcdn.com/images/pipiolo/post/f2d9939a-ad24-42df-8f87-d92b5a777742/image.png)

* 프리젠테이션 게층 `@Controller`
  * UI 관련 처리
  * 웹 요청∙응답
  * 기술 : 서블릿 등 HTTP 웹 기술, Spring MVC
* 서비스 계층 `@Service`
  * 비지니스 로직
  * 기술 : 특정 기술에 의존하지 않는 순수 자바 코드
* 데이터 접근 계층 `@Repository`
  * 데이터베이스에 접근
  * 기술 : JDBC, JPA, Redis

## 서비스 계층은 순수해야한다.
```java
@Service
public class Service {
    private Repository repositry;
    private DataSource dataSource;

    public void save(Entity e) throws SQLException {
        Connection con = dataSource.getConnection();
        try {
            con.setAutoCommit(false); // transaction start
            repository.save(e, con);  // bizLogic
            con.commit();             // commit
        } catch (Exception e) {
            com.rollback();           // rollback
            throw new IllegalStateException(e);
        } finally {
            con.setAutoCommit(true);  // Connection Pool
            con.close();
        }
    }
}
```

* 해당 코드는 다음과 같은 문제점을 가지고 있다.
* **서비스 계층은 순수해야 한다.**
  * 특정 기술에 의존하지 않고, 순수 자바 코드로만 작성해야 한다.
  * 프리젠테이션 계층은 UI와 관련된 기술들로부터 서비스 계층을 보호한다.
  * 데이터 접근 계층은 데이터 접근 기술로부터 서비스 계층을 보호한다.
    * `SQLException` JDBC 예외 같은 **특정 기술에 의존하고 있다.**
* 트랜잭션 동기화 문제
  * 트랜잭션은 서비스 계층에서 시작하는데, 데이터 베이스 접근은 리포지토리에서 이루어진다.
  * **서비스 계층의 커넥션과 리포지토리의 커넥션을 동기화 시켜야 한다.**
    * 파라미터로 커넥션을 넘겨주고 있다.
* 트랜잭션 적용 반복 문제
  * 트랜잭션을 적용할 때 마다 `try ~ catch ~ finally` 를 반복해야 한다.
  * 핵심 비지니스 로직보다 다른 로직이 더 많다.
    
## 트랜잭션 매니저 (트랜잭션 추상화)
![](https://velog.velcdn.com/images/pipiolo/post/61753f46-d2a5-4eec-b6d0-af0f98b020f8/image.png)

* `PlatformTransactionManager` 트랜잭션 매니저를 통해 추상화했다.
  * JDBC, JPA, Hibernate 등 다양한 트랜잭션 구현체를 갈아끼우면 된다.
  * 기술 변경 시, 의존관계 주입만 바꾸면 된다.
* 서비스 계층은 순수하다.
  * 서비스 계층을 데이터 접근 기술로부터 보호할 수 있다.
  * 추상화를 통해 서비스 계층은 JDBC, JPA 같은 특정 기술에 의존하지 않아도 된다.
    * `SQLException` 에외 관련 기술 의존은 아직 해결하지 못 했다.


## 트랜잭션 동기화 매니저
![](https://velog.velcdn.com/images/pipiolo/post/b48ead89-c751-4591-bbca-a7b087fadcb1/image.png)

* 트랜잭션을 유지하려면 같은 커넥션을 유지(`동기화`) 해야 한다.
* 트랜잭션 동기화 매니저를 통해 같은 커넥션을 유지할 수 있다.
  * 내부에서 쓰레드 로컬(`ThreadLocal`)을 통해 커넥션을 동기화한다.
    * 멀티 쓰레드 환경에서 안전하게 동작한다.
  * 커넥션을 파라미터로 전달하지 않아도 된다.
* 트랜잭션은 다음과 같이 동작한다.
  1. 서비스 계층에서 트랜잭션을 시작한다. 
  2. 트랜잭션 매니저는 데이터 소스를 사용해서 커넥션 풀을 통해 커넥션을 얻는다.
  3. 커넥션을 수동 커밋 모브도 변경해서 트랜잭션 동기화 매니저에 보관한다.
  4. 트랜잭션 동기화 매니저는 쓰레드 로컬에 커넥션을 보관한다.
  5. **리포지토리는 트랜잭션 동기화 매니저을 통해 커넥션을 얻는다.**
  6. 이를 통해 **같은 커넥션, 같은 트랜잭션을 유지**한다.
  7. 서비스 계층에서 트랜잭션을 종료한다.
  8. 트랜잭션 매니저는 트랜잭션 동기화 매니저 쓰레드 로컬에 보관된 커넥션을 가지고 온다.
  9. 획득한 커넥션을 통해 데이터베이스에 커밋하거나 롤백한다.
  10. 전체 리소스를 정리한다.
      * 트랜잭션 동기화 매니저의 쓰레드 로컬을 정리한다.
      * 자동 커밋 모드로 되돌린다. 커넥션 풀을 고려해야 한다.
      * 커넥션을 커넥션 풀에 반환한다.

## 트랜잭션 템플릿
```java
@Service
public class Service {
	
    private final TransactionTemplate txTemplate;
    
    public void service(String fromId, String toId, Integer money) {
    	txTemplate.executeWithoutResult((status) -> {
			try {
				bizLogic(fromId, toId, money);
    		} catch (SQLException e) {
         		throw new IllegalStateException(e);
    		}
		});
    }
} 



```

* `TransactionTemplate` 을 통해 트랜잭션 시작, 커밋, 롤백 하는 코드를 제거했다.
  * 비지니스 로직 결과에 따라 자동으로 커밋 혹은 롤백한다.
* `try ~ catch` 는 `SQLException` 체크 예외를 해결하기 위함이다.
  * 트래잭션 추상화로도 해결하지 못 한 특정 기술에 의존하는 문제이다.

## 한계
* 트랜잭션 추상화, 동기화, 템플릿을 통해 많은 문제들을 해결했다.
* 하지만, 서비스 계층에 비지니스 로직뿐만 아니라 트랜잭션 기술 로직이 함께 포함되어 있다.
  * 비지니스 로직은 핵심기능 이고, 트랜잭션 로직은 부가기능이다.
  * 두 관심사가 한 곳에 있으면, 유지보수하기 어렵다.
* 서비스 계층은 핵심 비지니스 로직만 있어야 한다.

## 트랜잭션 AOP
```java
public class TransactionProxy {
	
    private final Service target;
    private fianl TransactionManager transactionManager;
    
    public void logic() {
    	TransactionStatus status = transactionManager.getTransaction(..);
        try {
        	target.logic();
            transactionManager.commit(status);
        } catch (Exception e) {
        	transactionManager.rollback(status);
            throw new IllegalStateExcepeion(e);
        }
    }
}
```
```java
@Service
public class Service {

	public void logic() {
    	bizLogic();
    }
}
```
![](https://velog.velcdn.com/images/pipiolo/post/d0d0ec8d-ddfd-45b1-8ecc-a1c01d17e5a6/image.png)

* 기존에는 서비스 계층에 트랜잭션과 비지니스 로직이 섞여 있었다.
* 트랜잭션 프록시가 트랜잭션 로직을 담당하고 서비스 계층은 비지니스 로직만 책임진다.
  * 프록시를 통해 서비스 계층은 이제 순수하다.
  
### 스프링 트랜잭션 AOP
```java
@Service
public class Service {
	
    @Transactional
	public void logic() {
    	bizLogic();
    }
}
```
* `@Transactional` 애노테이션을 통해 프록시를 매우 편리하게 적용할 수 있다.
* 트랜잭션 프록시 클래스가 자동으로 생성된다.

![](https://velog.velcdn.com/images/pipiolo/post/9bf5f90b-101a-4a9f-89b4-6bfeef052298/image.jpeg)

---
