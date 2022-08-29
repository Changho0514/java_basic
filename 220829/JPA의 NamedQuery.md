# JPA의 NamedQuery

### 실무에서 쓸 일은 거의 없습니다

**@NamedQuery 어노테이션으로 Named 쿼리 정의***

[`Member.java`](http://Member.java) 위에

![Untitled](JPA%E1%84%8B%E1%85%B4%20NamedQuery%20e0af9b34ddd248c9b5f3ad1c692c51cc/Untitled.png)

써주고, 호출은

`MemberJpaRepository.java`

```java
public List<Member> findByUsername(String username) {

		List<Member> resultList =
		em.createNamedQuery("Member.findByUsername", Member.class)
		.setParameter("username", username)
		.getResultList();
}
```

`MemberJpaRepositoryTest.java`

```java
@Test
public void testNamedQuery() {
    Member m1 = new Member("AAA", 10);
    Member m2 = new Member("BBB", 20);

    memberJpaRepository.save(m1);
    memberJpaRepository.save(m2);

    List<Member> result = memberJpaRepository.findByUsername("AAA");
    Member findMember = result.get(0);
		assertThat(findMember).isEqualTo(m1);
}
```

**JPQL은 다음과 같다.**

![Untitled](JPA%E1%84%8B%E1%85%B4%20NamedQuery%20e0af9b34ddd248c9b5f3ad1c692c51cc/Untitled%201.png)

SpringData JPA에서 사용하려면 다음과 같이 하면 된다.

`@Query(name = "Member.findByUsername")
List<Member> findByUsername(@Param("username") String username);`

![Untitled](JPA%E1%84%8B%E1%85%B4%20NamedQuery%20e0af9b34ddd248c9b5f3ad1c692c51cc/Untitled%202.png)

**@Param** 이 필요한 이유는 [Member.java](http://Member.java) 상단에 

```java
@NamedQuery(
        name="Member.findByUsername",
        query="select m from Member m where m.username = :username")
```

쿼리가 `:username` 으로 JPQL을 명확하게 전달했으니 **namedParameter**를 넣어줘야하기때문에

Test를 해보려면

```java
@Test
public void testNamedQuery() {
    Member m1 = new Member("AAA", 10);
    Member m2 = new Member("BBB", 20);

    memberRepository.save(m1);
    memberRepository.save(m2);

    List<Member> result = memberRepository.findByUsername("AAA");
    Member findMember = result.get(0);
		assertThat(findMember).isEqualTo(m1);
}
```

- 스프링 데이터 JPA는 선언한 "도메인 클래스 + .(점) + 메서드 이름"으로 Named 쿼리를 찾아서 실행
- 만약 실행할 Named 쿼리가 없으면 메서드 이름으로 쿼리 생성 전략을 사용한다.
필요하면 전략을 변경할 수 있지만 권장하지 않는다.
참고: [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
#repositories.query-methods.query-lookup-strategies

> 참고: 스프링 데이터 JPA를 사용하면 실무에서 Named Query를 직접 등록해서 사용하는 일은 드물다.
**대신 @Query 를 사용해서 리파지토리 메소드에 쿼리를 직접 정의한다**
>