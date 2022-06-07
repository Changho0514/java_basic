# JPA와 DB 설정, 동작확인

세팅 시작

설정파일을 만들어주자. `resources→application.properties` 를 `application.yml` 파일로 만들어준다.

- yml 혹은 properties 둘다 사용해도 좋지만, 김영한님은 설정할 파일이 많아질수록 yml 이 편하다고 한다.

**application.yml**

```java
spring:
  datasource:
    url: jdbc:h2:tcp:/localhost/~/jpashop;MVCC=True
    username: sa
    password:
    driver-class-name: org.h2.Driver

  jpa:
    hibrernate:
      ddl-auto: create
    properties:
      hibernate:
        show_sql: true
        format_sql: true
logging:
    level:
      org.hibernate.SQL: debug
```

    `url: jdbc:h2:tcp:/localhost/~/jpashop;MVCC=True`

MVCC=True 옵션은 여러개가 한번에 접근했을 때 더 빨리 처리된다. (권장)

`dirver-class-name: org.h2.Driver` Database 커넥션과 관련된 데이터 소스 설정이 완료된다.

`jpa → hibernate → ddl-auto:true` : 어플리케이션 실행 시점에 내가 가지고 있는 테이블을 지우고 다시 생성하는 것. 

### 이런 설정은 어디가서 배우나요?

→ 스프링부트 메뉴얼에 들어가서 파야해요 [스프링 부트 레퍼런스 가이드]

참고: 모든 로그 출력은 가급적 로거를 통해 남겨야 한다.

> `show_sql` : 옵션은 System.out 에 하이버네이트 실행 SQL을 남긴다.
`org.hibernate.SQL` : 옵션은 logger를 통해 하이버네이트 실행 SQL을 남긴다.
> 

## 실행 되는지 확인해보기

**Member.class**

```java
package jpabook.jpashop;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Getter @Setter
public class member {

    @Id
    @GeneratedValue
    private Long id;
    private String username;

}

```

**MemberRepository.class**

```java
package jpabook.jpashop;

import org.springframework.data.annotation.PersistenceConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Repository
public class MemberRepository {

    @PersistenceContext
    private EntityManager em;

    public Long save(Member member) {
        em.persist(member);
        return member.getId();
    }
		public Member find(Long id) {
        return em.find(Member.class, id);
    }
}
```

**@Repository 는** Spring 실행시 컴포넌트 스캔의 대상이 되는 annotation중 하나

**jpa를 사용하기 때문에 Entity 매니저가 있어야 한다.**

→ @PersistenceContext

스프링을 사용하면 모두 스프링 컨테이너 위에서 실행되기 때문에 @PersistenceContext 가 있으면 자동으로 엔티티 매니저를 주입해준다.

### 근데 왜 member 반환안하고 getId()로 id만 반환할까?

→ 김영한님 스타일. **쿼리와 커맨드를 분리해라**  원칙에 의해서 저장하고 나면 가급적이면 사이드 이펙트 일으키는 커맨드성 코드이기 때문에 member값을 거의 직접적으로 쓰지 않는다. 대신 id값 있으면 다음에 다시 조회하면 되니까. id 정도만 조회하는걸로

`shift + command + T` 로 테스트 코드도 만들어주자

**MemberRepositoryTest**

```java
package jpabook.jpashop;

import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import static org.junit.Assert.*;

@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {
		@Autowired
    MemberRepository memberRepository;

    @Test
    public void testMember() throws Exception {
        //given "멤버를 가지고"
        Member member = new Member();
        member.setUsername("memberA");

        //when "save를 하면"
        Long saveId = memberRepository.save(member);
        Member findMember = memberRepository.find(saveId);

        //then "저장된 Id와 Searching한 Id가 일치하는지"
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
    }

}
```

`@SpringBootTest` → 스프링부트로 테스트하니까요. 

`@Runwith(SpringRunner.class)` → “스프링과 관련된걸로 테스트 할거야”

위의 코드를 실행하면 
에러가 나는데, 그 이유는 **엔티티 매니저를 통한 모든 데이터 변경은 트랜잭션을 통해서** 이루어 져야한다는것!

그래서 트랜잭션을 추가해주어야 한다.

**@Transaction** 추가