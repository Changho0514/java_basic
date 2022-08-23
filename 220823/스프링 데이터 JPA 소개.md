# 스프링 데이터 JPA 소개

- [https://spring.io/projects/spring-data-jpa](https://spring.io/projects/spring-data-jpa)

스프링 데이터 JPA는 JPA를 사용할 때 지루하게 반복하는 코드를 자동화 해준다. 이미 라이브러리는 포함되어 있다. 기존의 `MemberRepository` 를 스프링 데이터 JPA로 변경해보자.

우선 라이브러리가 필요하다.

`implementation 'org.springframework.boot:spring-boot-starter-data-jpa'`

```java
package jpabook.jpashop.repository;
  import jpabook.jpashop.domain.Member;
  import lombok.RequiredArgsConstructor;
  import org.springframework.stereotype.Repository;
  import javax.persistence.EntityManager;
  import java.util.List;
  @Repository
  @RequiredArgsConstructor
  public class MemberRepository {
      private final EntityManager em;
      public void save(Member member) {
          em.persist(member);
}
      public Member findOne(Long id) {
          return em.find(Member.class, id);
}
      public List<Member> findAll() {
          return em.createQuery("select m from Member m", Member.class)
                  .getResultList();
}
      public List<Member> findByName(String name) {
          return em.createQuery("select m from Member m where m.name = :name",
Member.class)
	} 
}

```

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%20d7b467fc200543f393d66b75c578b312/Untitled.png)

기존 [`MemberRepository.java`](http://MemberRepository.java) 를 `[MemberRepositoryOld.java](http://MemberRepositoryOld.java)` 로 살려놓고, (Rename)

`MemberRepository.interface` 를 만든다.

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%20d7b467fc200543f393d66b75c578b312/Untitled%201.png)

그럼 [`MemberService.java`](http://MemberService.java) 에 가보면 
`MemberRepository Interface`로 바뀌어있다. 

`CrudRepository.java`

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%20d7b467fc200543f393d66b75c578b312/Untitled%202.png)

`CrudRepository.java` 에서는 findOne()을 지원하지 않으니`findOne()` → `findById()`로 변경해야 한다.

```java
/**
 *한건 조회
*/
public Member findOne(Long memberId) {
    return memberRepository.findById(memberId);
}

@Transactional
public void update(Long id, String name) {
    Member member = memberRepository.findById(id);
    member.setName(name);
}
```

SpringData JPA는 

`Optional`로 반환되므로 뒤에 `.get()` 도 붙여주자

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%20d7b467fc200543f393d66b75c578b312/Untitled%203.png)

해결 안되는건 `findByName` 이 남아있는데, 

Name으로 조회하는건 특수한 경우잖아.

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
    List<Member> findByName(String name);
}
```

이렇게만 적어도 알아서 만들어준다…………

`findByOOO` → 이게 공식과 비슷한 거라서 SpringData JPA가 알아서 구현해주는거에요.

- 스프링 데이터 JPA는 `JpaRepository` 라는 인터페이스를 제공하는데, 여기에 기본적인 CRUD 기능이 모두 제공된다. (일반적으로 상상할 수 있는 모든 기능이 다 포함되어 있다.)
- `findByName` 처럼 일반화 하기 어려운 기능도 메서드 이름으로 정확한 JPQL 쿼리를 실행한다.
    - `select m from Member m where [m.name](http://m.name/) = :name`
- **개발자는 인터페이스만 만들면 된다. 구현체는 스프링 데이터 JPA가 애플리케이션 실행시점에 주입해준다.**

스프링 데이터 JPA는 스프링과 JPA를 활용해서 애플리케이션을 만들때 정말 편리한 기능을 많이 제공한다. 단순히 편리함을 넘어서 때로는 마법을 부리는 것 같을 정도로 놀라운 개발 생산성의 세계로
우리를 이끌어 준다.
하지만 **스프링 데이터 JPA는 JPA를 사용해서 이런 기능을 제공할 뿐이다. 결국 JPA 자체를 잘 이해하는 것이 가장 중요**하다.