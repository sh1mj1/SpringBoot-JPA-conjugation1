# SpringBoot-JPA-conjugation1

# ==== 프로젝트 환경설정 ====

# 1. 프로젝트 생성

[https://start.spring.io/](https://start.spring.io/)을 이용합니다.

**프로젝트 선택**

- Project: Gradle Project
- Language: Java
- Spring Boot: 2.4.x
- Packaging: Jar
- Java: 11
- Dependencies: Spring Web, Thymeleaf, Spring Data JPA, H2, Lombok, Validation

- Group: jpabook
- Name: jpashop

# 2. 라이브러리 살펴보기

이 프로젝트에는 아래와 같은 라이브러리가 포함됩니다.

### 스프링 부트 라이브러리

- spring-boot-starter-web
    - spring-boot-starter-tomcat: 톰캣 (웹서버)
    - spring-webmvc: 스프링 웹 MVC

- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
- spring-boot-starter-data-jpa
    - spring-boot-starter-aop
    - spring-boot-starter-jdbc
        - HikariCP 커넥션 풀 (부트 2.0 기본)

- hibernate + JPA: 하이버네이트 + JPA
- spring-data-jpa: 스프링 데이터 JPA

- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
    - spring-boot
        - spring-core
    
- spring-boot-starter-logging
    - logback, slf4j

### **테스트 라이브러리**

- spring-boot-starter-test
    - junit: 테스트 프레임워크
    - mockito: 목 라이브러리
    - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
    - spring-test: 스프링 통합 테스트 지원

**핵심 라이브러리**

- 스프링 MVC
- 스프링 ORM
- JPA, 하이버네이트
- 스프링 데이터 JPA

**기타 라이브러리**

- H2 데이터베이스 클라이언트
- 커넥션 풀: 부트 기본은 HikariCP
- WEB(thymeleaf)
- 로깅 SLF4J & LogBack
- 테스트

> 참고: 스프링 데이터 JPA는 스프링과 JPA를 먼저 이해하고 사용해야 하는 응용기술입니다.
> 

# 3. View 환경 설정

View 는 thymeleaf 템플릿 엔진을 사용합니다.

### **thymeleaf 템플릿 엔진**

thymeleaf 공식 사이트: https://www.thymeleaf.org/

스프링 공식 튜토리얼: https://spring.io/guides/gs/serving-web-content/

스프링부트 메뉴얼: https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-templateengines

스프링 부트 thymeleaf 는 viewName 으로 매핑할 수 있습니다.

- `resources:templates/` +{ViewName}+ `.html`

```java
@Controller
public class HelloController {
    
    @GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "hello!!");
        return "hello";
    }
}
```

`resources/templates/hello.html`

```java
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>
</body>
</html>
```

[http://localhost:8080/hello](http://localhost:8080/hello)

![Screenshot 2023-02-25 at 6.37.31 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d2ae184b-0bfb-48a7-abc5-7cbaa6085ba1/Screenshot_2023-02-25_at_6.37.31_PM.png)

> 참고- spring-boot-devtools 라이브러리를 추가하면 html 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능합니다.
> 

# 4. H2 데이터베이스 설치

개발이나 테스트 용도로 가볍고 편리한 DB와 웹 화면을 제공합니다.

https://www.h2database.com

- 위 링크에서 다운로드 및 설치.

맥북 기준 ./h2.sh 로 실행.

![Screenshot 2023-02-25 at 6.40.07 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2da3b0ee-4ea9-45f5-a95e-bff8adad1bb0/Screenshot_2023-02-25_at_6.40.07_PM.png)

### 데이터베이스 파일 생성 방법

`jdbc:h2:~/jpashop` (최소 한번)

![https://user-images.githubusercontent.com/52024566/128595045-7d5110e5-18bd-41f0-bbc8-9b9faf60b1b4.png](https://user-images.githubusercontent.com/52024566/128595045-7d5110e5-18bd-41f0-bbc8-9b9faf60b1b4.png)

`/jpashop.mv.db` 파일 생성 확인

이후부터는 `jdbc:h2:tcp://localhost/~/jpashop` 이렇게 접속하면 됩니다.

![https://user-images.githubusercontent.com/52024566/128595046-85bc860d-8807-47ca-9336-fa5f6f4c39f6.png](https://user-images.githubusercontent.com/52024566/128595046-85bc860d-8807-47ca-9336-fa5f6f4c39f6.png)

> 참고 : -tcpPort 옵션을 통하여 H2 Database 실행 포트를 설정할 수 있음.
> 

![https://user-images.githubusercontent.com/52024566/128595047-af90d237-eeb7-4d8d-ade8-4776b0940eab.png](https://user-images.githubusercontent.com/52024566/128595047-af90d237-eeb7-4d8d-ade8-4776b0940eab.png)

> 주의: H2 데이터베이스의 MVCC 옵션은 H2 1.4.198 버전부터 제거되었습니다. 1.4.200 버전에서는 MVCC 옵션을 사용하면 오류가 발생합니다.
> 

# 5. JPA 와 DB 설정, 동작확인

`main/resources/application.yml`

```yaml
spring:
  datasource:
    url:
      jdbc:h2:tcp://localhost/~/jpashop
    username: sa
    password:
    driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
#        show_sql: true
        format_sql: true

logging:
  level:
    org.hibernate.sql: debug
#    org.hibernate.type: trace
```

`spring.jpa.hibernate.ddl-auto: create` 

이 옵션은 애플리케이션 실행 시점에 테이블을 drop 하고, 다시 생성합니다.

> 참고: 모든 로그 출력은 가급적 로거를 통해 남겨야 합니다. show_sql : 옵션은 System.out 에 하이버네이트 실행 SQL을 남깁니다. 
org.hibernate.SQL : 옵션은 logger를 통해 하이버네이트 실행 SQL을 남김.
> 

> 주의 - application.yml 같은 yml 파일은 띄어쓰기(스페이스) 2칸으로 계층을 만드므로 띄어쓰기 2칸을 필수로 적어주어야 한다. 
예를 들어서 아래의 datasource 는 spring: 하위에 있고 앞에 띄어쓰기 2칸이 있으므로 spring.datasource 가 된다.
> 

### **실제 동작하는지 확인하기**

**회원 엔티티**

```java
@Entity
@Getter @Setter
public class Member {
    
    @Id @GeneratedValue
    private Long id;
    private String username;
}
```

**회원 리포지토리**

```java
@Repository
public class MemberRepository {
    
    @PersistenceContext
    EntityManager em;
    
    public Long save(Member member) {
        em.persist(member);
        return member.getId();
    }
    
    public Member find(Long id) {
        return em.find(Member.class, id);
    }
}
```

**테스트**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {
    
    @Autowired MemberRepository memberRepository;
    
    @Test
    @Transactional
    @Rollback(false)
    public void testMember() {
        // given
        Member member = new Member();
        member.setUsername("memberA");
        
        // when
        Long savedId = memberRepository.save(member);
        Member findMember = memberRepository.find(savedId);
        
        // then
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
        Assertions.assertThat(findMember).isEqualTo(member); //JPA 엔티티 동일성 보장
    }
}
```

> 주의! @Test는 JUnit4를 사용하면 org.junit.Test를 사용해야 한다.
> 

- Entity, Repository 동작 확인
- jar 빌드해서 동작 확인

```java
./gradlew.bat build
cd build
cd libs
java -jar jpashop-0.0.1-SNAPSHOT.jar
```

만약 테스트를 실행했는데 다음과 같이 테스트를 찾을 수 없는 오류가 발생하는 경우 

```java
No tests found for given includes: 
		[jpabook.jpashop.MemberRepositoryTest](filter.includeTestsMatching)
```

스프링 부트 2.1.x 버전을 사용하지 않고, 2.2.x 이상 버전을 사용하면 Junit5가 설치됩니다. 

이때는 build.gradle 마지막에 다음 내용을 추가하면 테스트를 인식할 수 있습니다. Junit5 부터는 build.gradle 에 다음 내용을 추가해야 테스트가 인식됩니다.

`build.gradle` 마지막에 추가

```java
test {
	useJUnitPlatform()
}
```

> 참고: 스프링 부트를 통해 복잡한 설정이 다 자동화 되었다. persistence.xml 도 없고, LocalContainerEntityManagerFactoryBean 도 없다.
> 

### **쿼리 파라미터 로그 남기기**

로그에 아래 코드를 추가하기 

`org.hibernate.type` : SQL 실행 파라미터를 로그로 남긴다.

- 외부 라이브러리 사용
    - https://github.com/gavlyukovskiy/spring-boot-data-source-decorator
        
        스프링 부트를 사용하면 이 라이브러리만 추가하면 된다. 
        

`implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.6'`

> 참고: 쿼리 파라미터를 로그로 남기는 외부 라이브러리는 시스템 자원을 사용하므로, 개발 단계에서는 편하게 사용해도 된다. 하지만 운영시스템에 적용하려면 꼭 성능테스트를 하고 사용하는 것이 좋다.
> 

이렇게 프로젝트 환경설정을 마쳤습니다. 다음 글에서 도메인부터 시작해서 프로젝트를 만들어 나가겠습니다.

# ==== 1. 도메인 분석 설계 ====

## 1. 요구사항 분석

![https://user-images.githubusercontent.com/52024566/128713633-4a131479-d068-464e-b853-58fe1c17836a.png](https://user-images.githubusercontent.com/52024566/128713633-4a131479-d068-464e-b853-58fe1c17836a.png)

우리는 위 그림과 같은 서비스를 만들어야 합니다. 

만들어야 하는 기능은 아래와 같습니다.

### **기능 목록**

- 회원 기능
    - 회원 등록
    - 회원 조회

- 상품 기능
    - 상품 등록
    - 상품 수정
    - 상품 조회

- 주문 기능
    - 상품 주문
    - 주문 내역 조회
    - 주문 취소

- 기타 요구사항
    - 상품은 재고 관리가 필요하다.
    - 상품의 종류는 도서, 음반, 영화가 있다.
    - 상품을 카테고리로 구분할 수 있다.
    - 상품 주문시 배송 정보를 입력할 수 있다.


## 2. **도메인 모델과 테이블 설계**

![https://user-images.githubusercontent.com/52024566/128713622-49b31691-b51b-41a6-9e4c-99d7a67294ab.png](https://user-images.githubusercontent.com/52024566/128713622-49b31691-b51b-41a6-9e4c-99d7a67294ab.png)

### **회원, 주문, 상품의 관계**

회원은 여러 상품을 주문할 수 있습니다. 그리고 한 번 주문할 때 여러 상품을 선택할 수 있으므로 주문과 상품은 다대다 관계입니다. 

하지만 이런 다대다 관계는 관계형 데이터베이스는 물론이고 엔티티에서도 거의 사용하지 않습니다. 따라서 그림처럼 주문상품이라는 엔티티를 추가해서 다대다 관계를 일대다, 다대일 관계로 풀어냈습니다. 

**상품 분류**

상품은 도서, 음반, 영화로 구분되는데 상품이라는 공통 속성을 사용하므로 상속 구조로 표현했습니다.

### **회원 엔티티 분석**

![https://user-images.githubusercontent.com/52024566/128713629-4feb764c-6ac0-491a-b2a7-9a1e7ceec70e.png](https://user-images.githubusercontent.com/52024566/128713629-4feb764c-6ac0-491a-b2a7-9a1e7ceec70e.png)

**회원(Member)**

이름과 임베디드 타입인 주소( `Address` ), 그리고 주문( `orders` ) 리스트를 가집니다. 

**주문(Order)**

한 번 주문시 여러 상품을 주문할 수 있으므로 주문과 주문상품( `OrderItem` )은 일대다 관계입니다. 

주문은 상품을 주문한 회원과 배송 정보, 주문 날짜, 주문 상태( `status` )를 가지고 있습니다. 주문 상태는 열거형을 사용했는데 주문( `ORDER`), 취소( `CANCEL` )을 표현할 수 있습니다.

**주문상품(OrderItem)**

주문한 상품 정보와 주문 금액( `orderPrice` ), 주문 수량( `count` ) 정보를 가지고 있습니다. 

(보통 `OrderLine` , `LineItem` 으로 많이 표현한다.) 

**상품(Item)**

이름, 가격, 재고수량( `stockQuantity` )을 가지고 있습니다. 상품을 주문하면 재고수량이 줄어듭니다. 상품의 종류로는 도서, 음반, 영화가 있는데 각각은 사용하는 속성이 조금씩 다릅니다. 

**배송(Delivery)**

주문시 하나의 배송 정보를 생성합니다. 주문과 배송은 일대일 관계입니다. 

**카테고리(Category)**

상품과 다대다 관계를 맺습니다. `parent` , `child` 로 부모, 자식 카테고리를 연결합니다.

**주소(Address)**

값 타입(임베디드 타입)입니다. 회원과 배송(`Delivery`)에서 사용합니다.

> 참고 - 회원이 주문을 하기 때문에, 회원이 주문리스트를 가지는 것은 얼핏 보면 잘 설계한 것 같지만, 객체 세상은 실제 세계와는 다릅니다. 
실무에서는 회원이 주문을 참조하지 않고, 주문이 회원을 참조하는 것으로 충분합니다. 여기서는 일대다, 다대일의 양방향 연관관계를 설명하기 위해서 추가했습니다.
> 

### **회원 테이블 분석**

![https://user-images.githubusercontent.com/52024566/128713631-81b730d6-754a-4ac5-9310-05915a063a4a.png](https://user-images.githubusercontent.com/52024566/128713631-81b730d6-754a-4ac5-9310-05915a063a4a.png)

**MEMBER**

회원 엔티티의 `Address` 임베디드 타입 정보가 회원 테이블에 그대로 들어갔습니다. 이것은 `DELIVERY` 테이블도 마찬가지입니다. 

**ITEM**

앨범, 도서, 영화 타입을 통합해서 하나의 테이블로 만들었습니다. `DTYPE` 컬럼으로 타입을 구분합니다.

> 참고: 테이블명이 ORDER 가 아니라 ORDERS 인 것은 데이터베이스가 order by 때문에 예약어로 잡고 있는 경우가 많다. 그래서 관례상 ORDERS 를 많이 사용한다.
> 

### **연관관계 매핑 분석**

**회원과 주문**

일대다 , 다대일의 양방향 관계입니다. 따라서 연관관계의 주인을 정해야 하는데, 외래 키가 있는 주문을 연관관계의 주인으로 정하는 것이 좋습니다. 그러므로 `Order.member` 를 `ORDERS.MEMBER_ID` 외래 키와 매핑합니다. 

**주문상품과 주문**

다대일 양방향 관계입니다. 외래 키가 주문상품에 있으므로 주문상품이 연관관계의 주인입니다. 그러므로 `OrderItem.order` 를 `ORDER_ITEM.ORDER_ID` 외래 키와 매핑합니다. 

**주문상품과 상품**

다대일 단방향 관계입니다. `OrderItem.item` 을 `ORDER_ITEM.ITEM_ID` 외래 키와 매핑합니다. 

**주문과 배송**

일대일 양방향 관계입니다. `Order.delivery` 를 `ORDERS.DELIVERY_ID` 외래 키와 매핑합니다. 

**카테고리와 상품**

`@ManyToMany` 를 사용해서 매핑한다.(실무에서 `@ManyToMany`는 사용하지 않는 게 좋습니다. 여기서는 다대다 관계를 예제로 보여주기 위해 추가했을 뿐입니다)

> 참고 - 외래 키가 있는 곳을 연관관계의 주인으로 정하는 게 좋습니다.
> 

**연관관계의 주인은 단순히 외래 키를 누가 관리하냐의 문제**이지 비즈니스상 우위에 있다고 주인으로 정하면 안됩니다. 예를 들어서 자동차와 바퀴가 있으면, 일대다 관계에서 항상 다쪽에 외래 키가 있으므로 외래 키가 있는 바퀴를 연관관계의 주인으로 정하면 됩니다. 물론 자동차를 연관관계의 주인으로 정하는 것이 불가능 한 것은 아니지만, 자동차를 연관관계의 주인으로 정하면 자동차가 관리하지 않는 바퀴 테이블의 외래 키 값이 업데이트 되므로 관리와 유지보수가 어렵고, 추가적으로 별도의 업데이트 쿼리가 발생하는 성능 문제도 있습니다.

### **연관관계 정의 규칙**

방향 : 단방향, 양방향

A 객체가 참조용 필드를 갖고 있어 다른 B 객체를 참조할 수 있을 때 A → B 로 방향이 결정됩니다.

연관 관계의 주인 : 양방향일 때, 연관 관계에서의 관리 주체를 말합니다.

- 연관 관계의 주인은 두 객체 사이에서 조회, 저장, 수정, 삭제가 가능하지만 주인이 아니면 조회만 가능합니다.

다중성 : 다대일(N:1), 일대다(1:N), 일대일(1:1), 다대다(N:M)

- 다대다(N:M)은 중간 테이블이 숨겨져 있기 때문에 개발자가 모르는 복잡한 조인 쿼리가 발생할 수 있습니다.
- 자동 생성된 중간 테이블에는 두 객체 테이블의 외래 키만 저장되기 때문에 문제가 될 확률이 높습니다.
- 중간 테이블에 외래 키 외에 다른 정보를 넣어야 할 경우가 많기 때문에 다대다를 일대다, 다대일로 풀어서 만들어야 변경 사항에도 유연하게 대응할 수 있습니다.

## 3. **엔티티 클래스 개발**

예제에서는 설명을 쉽게하기 위해 엔티티 클래스에 `Getter`, `Setter`를 모두 열고, 최대한 단순하게 설계했습니다.

실무에서는 가급적 Getter는 열어두고, Setter는 꼭 필요한 경우에만 사용하는 것을 추천합니다.

> 참고 - 이론적으로 Getter, Setter 모두 제공하지 않고, 꼭 필요한 별도의 메서드를 제공하는게 가장 이상적입니다. 하지만 실무에서 엔티티의 데이터는 조회할 일이 너무 많으므로, Getter의 경우 모두 열어두는 것이 편리합니다. Getter는 아무리 호출해도 호출 하는 것 만으로 어떤 일이 발생하지는 않습니다. 하지만 Setter를 호출하면 데이터가 변합니다. Setter를 막 열어두면 가까운 미래에 엔티티에가 도대체 왜 변경되는지 추적하기 점점 힘들어집니다. 그래서 엔티티를 변경할 때는 Setter 대신에 변경 지점이 명확하도록 변경을 위한 비즈니스 메서드를 별도로 제공해야 합니다.
> 

**회원 엔티티**

```java
package jpabook.jpashop.domain;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter @Setter
public class Member {
    
    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    
    private String name;
    
    @Embedded
    private Address address;
    
    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();
}
```

엔티티의 식별자는 `id` 를 사용하고 PK 컬럼명은 `member_id` 를 사용했습니다. 엔티티는 타입(여기서는 `Member` )이 있으므로 `id` 필드만으로 쉽게 구분할 수 있지만 테이블은 타입이 없으므로 구분이 어렵다. 

그리 고 테이블은 관례상 `테이블명 + id` 를 많이 사용합니다. 참고로 객체에서 `id` 대신에 `memberId` 를 사용해도 됩니다. 중요한 것은 일관성입니다.

주문 엔티티 `Order`

```java
@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {
    
    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member; //주문 회원
    
    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();
    
    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    @JoinColumn(name = "delivery_id")
    private Delivery delivery; //배송정보
    
    private LocalDateTime orderDate; //주문시간
    
    @Enumerated(EnumType.STRING)
    private OrderStatus status; //주문상태 [ORDER, CANCEL]
    
    //==연관관계 메서드==//
    public void setMember(Member member) {
        this.member = member;
        member.getOrders().add(this);
    }
    
    public void addOrderItem(OrderItem orderItem) {
        orderItems.add(orderItem);
        orderItem.setOrder(this);
    }
    
    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
        delivery.setOrder(this);
    }
}
```

주문상태 - `OrderStatus`

```java
public enum OrderStatus {
    ORDER, CANCEL
}
```

주문상품 엔티티 `OrderItem`

```java
@Entity
@Table(name = "order_item")
@Getter @Setter
public class OrderItem {
    
    @Id @GeneratedValue
    @Column(name = "order_item_id")
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "item_id")
    private Item item; //주문 상품
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "order_id")
    private Order order; //주문
    
    private int orderPrice; //주문 가격
    private int count; //주문 수량
}
```

상품 엔티티 `Item`

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "dtype")
@Getter @Setter
public abstract class Item {
    
    @Id @GeneratedValue
    @Column(name = "item_id")
    private Long id;
    
    private String name;
    private int price;
    private int stockQuantity;
    
    @ManyToMany(mappedBy = "items")
    private List<Category> categories = new ArrayList<Category>();
}
```

상품 - 도서 엔티티  `Book`

```java
@Entity
@DiscriminatorValue("B")
@Getter @Setter
public class Book extends Item {
    
    private String author;
    private String isbn;
}
```

상품 - 음반 엔티티 `Album`

```java
@Entity
@DiscriminatorValue("A")
@Getter @Setter
public class Album extends Item {
    
    private String artist;
    private String etc;
}
```

상품 - 영화 엔티티 `Movie`

```java
@Entity
@DiscriminatorValue("M")
@Getter @Setter
public class Movie extends Item {
    
    private String director;
    private String actor;
}
```

배송 엔티티 `Delivery`

```java
@Entity
@Getter @Setter
public class Delivery {
    @Id @GeneratedValue
    @Column(name = "delivery_id")
    private Long id;
    
    @OneToOne(mappedBy = "delivery", fetch = FetchType.LAZY)
    private Order order;
    
    @Embedded
    private Address address;
    
    @Enumerated(EnumType.STRING)
    private DeliveryStatus status; //ENUM [READY(준비), COMP(배송)]
}
```

배송 상태 `DeliveryStatus`

```java
public enum DeliveryStatus {
	READY, COMP
}
```

카테고리 엔티티 `Category`

```java
@Entity
@Getter @Setter
public class Category {
    
    @Id @GeneratedValue
    @Column(name = "category_id")
    
    private Long id;
    private String name;
    
    @ManyToMany
    @JoinTable(name = "category_item",
               joinColumns = @JoinColumn(name = "category_id"),
               inverseJoinColumns = @JoinColumn(name = "item_id"))
    private List<Item> items = new ArrayList<>();
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "parent_id")
    private Category parent;
    
    @OneToMany(mappedBy = "parent")
    private List<Category> child = new ArrayList<>();
    
    //==연관관계 메서드==//
    public void addChildCategory(Category child) {
        this.child.add(child);
        child.setParent(this);
    }
}
```

실제 실무에서는 `@ManyToMany` 를 사용하지 않는 것이 좋습니다. 

`@ManyToMany` 는 편리한 것 같지만, 중간 테이블( `CATEGORY_ITEM` )에 컬럼을 추가할 수 없고, 세밀하게 쿼리를 실행하기 어렵기 때문에 실무에서 사용하기에는 한계가 있습니다. 중간 엔티티( `CategoryItem` 를 만들고 `@ManyToOne` , `@OneToMany` 로 매핑해서 사용합시다. 정리하면 대다대 매핑을 일대다, 다대일 매핑으로 풀어내서 사용해야 합니ㅏㄷ.

주소 값 타입 `Address`

```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.Embeddable;

@Embeddable
@Getter
public class Address {
    
    private String city;
    private String street;
    private String zipcode;
    
    protected Address() {
    }
    
    public Address(String city, String street, String zipcode) {
        this.city = city;
        this.street = street;
        this.zipcode = zipcode;
    }
}
```

설계 시 값 타입은 변경 불가능하게 설계해야 합니다. @Setter 를 제거하고, 생성자에서 값을 모두 초기화해서 변경 불가능한 클래스를 만듭니다. 

JPA 스펙상 엔티티나 임베디드 타입( `@Embeddable` )은 자바 기본 생성자(default constructor)를 `public` 또는 `protected` 로 설정해야 합니다. `public` 으로 두는 것 보다는 `protected` 로 설정하는 것이 그나마 더 안전합니다. 

JPA가 이런 제약을 두는 이유는 JPA 구현 라이브러리가 객체를 생성할 때 리플랙션 같은 기술을 사용할 수 있도록 지원해야 하기 때문입니다.

## 4. **엔티티 설계시 주의점**

**엔티티에는 가급적 Setter를 사용하지 않는 게 좋습니다.**

Setter 가 모두 열려있다. 변경 포인트가 너무 많아서, 유지보수가 어렵습니다. 나중에 리펙토링으로 Setter 제거해야 합니다.

**모든 연관관계는 지연로딩으로 설정!**

즉시로딩( EAGER )은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기 어렵습니다다. 특히 JPQL을 실행할 때 N+1 문제가 자주 발생합니다.

실무에서 모든 연관관계는 지연로딩( `LAZY` )으로 설정해야 합니다.

연관된 엔티티를 함께 DB에서 조회해야 하면, `fetch join` 또는 엔티티 그래프 기능을 사용합니다.

`@XToOne` (`OneToOne` 과 `ManyToOne`) 관계는 기본이 즉시로딩이므로 직접 지연로딩으로 설정해야 합니다.

**컬렉션은 필드에서 초기화 하자.**

컬렉션은 필드에서 바로 초기화 하는 것이 안전합니다. 그리고 null 문제에서 안전하게 됩니다.

하이버네이트는 엔티티를 영속화 할 때, 컬랙션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경합니다. 아래처럼 말이죠.

```java
Member member = new Member();
System.out.println(member.getOrders().getClass());
em.persist(team);
System.out.println(member.getOrders().getClass());

//출력 결과
class java.util.ArrayList
class org.hibernate.collection.internal.PersistentBag
```

만약 `getOrders()` 처럼 임의의 메서드에서 컬력션을 잘못 생성하면 하이버네이트 내부 메커니즘에 문제가 발생할 수 있습니다. 따라서 필드레벨에서 생성하는 것이 가장 안전하고, 코드도 간결합니다.

**테이블, 컬럼명 생성 전략**

스프링 부트에서 하이버네이트 기본 매핑 전략을 변경해서 실제 테이블 필드명은 다르게 변합니다. 

하이버네이트 기존 구현: 엔티티의 필드명을 그대로 테이블의 컬럼명으로 사용 ( `SpringPhysicalNamingStrategy` )

스프링 부트 신규 설정 (엔티티(필드) 테이블(컬럼))

1. 카멜 케이스 →  언더스코어(memberPoint → member_point)
2. .(점) → _(언더스코어)
3. 대문자 → 소문자

**적용 2 단계**

1. 논리명 생성: 명시적으로 컬럼, 테이블명을 직접 적지 않으면 `ImplicitNamingStrategy` 사용. 
`spring.jpa.hibernate.naming.implicit-strategy` : 테이블이나, 컬럼명을 명시하지 않을 때 논리명 적용

2. 물리명 적용: `spring.jpa.hibernate.naming.physical-strategy` : 모든 논리명에 적용됨, 실제 테이블에 적용 (username → usernm 등으로 회사 룰로 바꿀 수 있음)

**스프링 부트 기본 설정**

```java
spring.jpa.hibernate.naming.implicit-strategy=
org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingStrategy
spring.jpa.hibernate.naming.physical-strategy=
org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy
```

