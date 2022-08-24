# 스프링 데이터 JPA와 DB 설정, 동작확인

`application.yml`

```
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/datajpa
    username: sa
   password:
    driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
# show_sql: true
format_sql: true
logging.level:
  org.hibernate.SQL: debug
# org.hibernate.type: trace
```

`spring.jpa.hibernate.ddl-auto: create`
이 옵션은 애플리케이션 실행 시점에 테이블을 drop 하고, 다시 생성한다
공부할때 좋음. 운영할때는 큰일나요..

> **참고: 모든 로그 출력은 가급적 로거를 통해 남겨야 한다**
> 

> **`show_sql` : 옵션은 System.out 에 하이버네이트 실행 SQL을 남긴다.**
`**org.hibernate.SQL**` : 옵션은 logger를 통해 하이버네이트 실행 SQL을 남긴다 → 이게 좋음!!
> 

`study.datajpa.entity.Member.java`

```java
package study.datajpa.entity;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Getter
@Setter
public class Member {

    @Id
    @GeneratedValue
    private Long id;
    private String username;
}
```

식별자 매핑을 위해 **@Id** 를 사용해주고,

**@GeneratedValue 는 PK값을 자동으로 설정해준다.**

`study.datajpa.repository.MemberJpaRepository.java`

```java
package study.datajpa.repository;

import org.springframework.stereotype.Repository;
import study.datajpa.entity.Member;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Repository
public class MemberJpaRepository {

    @PersistenceContext
    private EntityManager em;

    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    public Member find(Long id) {
        return em.find(Member.class, id);
    }

}

```

**@PersistenceContext**

→ JPA가 알아서 DB에 INSERT 쿼리를 날려서 저장하고,
엔티티 매니저라는 걸 갖다 준다. 즉, 영속성 컨텍스트로 관리할 수 있게 해준다.

### Test case 만들자

`command + shift + T`

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%E1%84%8B%E1%85%AA%20DB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC,%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%E1%84%92%E1%85%AA%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%2000b8e1ad1fa240c6bd308e4e8e25a9ca/Untitled.png)

Test를 하더라도 Spring Bean을 모두 Injection 받아서 사용할 것이기 때문에

**@SpringBootTest 를 위에 써준다.**

`@RunWith` 는 Junit5 이후로 안써도 된다!

**@Test 는** 

`import org.junit.jupiter.api.Test;` 이것 사용하는것 확인!

```java
@Test
public void testMember() {
    Member member = new Member();
    member.setUsername("memberA");

}
```

**사실 여기서 Setter를 빼고 생성자에서 파라미터를 넘겨서 하는것이 좋다.**

→ 그냥 그렇게 하자.

1. `[Member.java](http://Member.java)` 에 Constructor 생성해주고
2. `protected Member(){}` 하나 만들어준다 ← JPA이기 때문에 아무데서나 생성되지 않게 하기위해서. 프록시 객체 생성될때 private으로 하면 막히니까. 거기서는 생성되어야함

```java
package study.datajpa.repository;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import study.datajpa.entity.Member;

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class MemberJpaRepositoryTest {

    @Autowired
    MemberJpaRepository memberJpaRepository;

    @Test
    public void testMember() {
        Member member = new Member("memberA");
        Member savedMember = memberJpaRepository.save(member);

        Member findMember = memberJpaRepository.find(savedMember.getId());

assertThat(findMember.getId()).isEqualTo(member.getId());
assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
    }
}
```

이렇게 돌려보면 에러가 난다.

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%E1%84%8B%E1%85%AA%20DB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC,%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%E1%84%92%E1%85%AA%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%2000b8e1ad1fa240c6bd308e4e8e25a9ca/Untitled%201.png)

## JPA의 모든 데이터의 변경은 트랜잭션 안에서 일어나야 한다.

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%E1%84%8B%E1%85%AA%20DB%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC,%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%E1%84%92%E1%85%AA%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%2000b8e1ad1fa240c6bd308e4e8e25a9ca/Untitled%202.png)

`org.springframework.transaction.annotation` 의 **@Transaction 을 사용하도록 한다.**