# SpringBoot-JPA-conjugation1

- 프로젝트 생성
- 라이브러리 살펴보기
- View 환경 설정
- H2 데이터베이스 설치
- JPA 와 DB 설정, 동작확인

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