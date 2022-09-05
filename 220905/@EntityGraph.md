# @EntityGraph

## 연관된 엔티티들을 SQL 한번에 조회하는 방법

먼저 JPA의 fetch 조인에 대한 이해가 확실히 필요하다

`MemberRepositoryTest.java`

```java
@Test
public void findMemberLazy() {
    //given
    //member1 -> teamA 참조
    //member2 -> teamB 참조

    Team teamA = new Team("teamA");
    Team teamB = new Team("teamB");
    teamRepository.save(teamA);
    teamRepository.save(teamB);
    Member member1 = new Member("member1", 10, teamA);
    Member member2 = new Member("member2", 20, teamB);
    memberRepository.save(member1);
    memberRepository.save(member2);
}
```

member1이 teamA를 참조하고, member2이 teamB를 참조하게 Test를 하나 만든다.

`Member.java`를 보면,

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled.png)

Member → Team의 관계는 다대일관계. 
⇒ FetchType.LAZY로 무조건 걸어야함!

이렇게되면 Member 조회시 Team은 조회를 하지 않는다
프록시 객체를 하나 만들어 조회했다고 말만 해놓는다.
실제 Team의 데이터를 사용하는 시점에 Team을 꺼내기 위한 SQL이 별도로 날아간다.

다시 `MemberRepositoryTest.java`

findMemberLAZY 테스트 아래에

```java
em.flush();
em.clear();
```

를 추가해주면 **영속성 컨텍스트에 남아있던 캐시 정보를 전부 DB에 반영하여 INSERT를 다 하고, 영속성 컨텍스트를 깔끔하게 지운다.**

그리고, 멤버 객체를 가져와 for문을 돌려보자

```java
//when
List<Member> members = memberRepository.findAll();

for (Member member : members) {
    System.out.println("member = " + member.getUsername());
}
```

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%201.png)

추가가 잘 되겠죠!

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%202.png)

**findAll은 쿼리를 1번 날려 member1, member2를 모두 가져왔다**

하지만 for문을

```java
for (Member member : members) {
    System.out.println("member = " + member.getUsername());
    System.out.println("member.team = " + member.getTeam().getName());
}
```

Team에 대한 정보까지 같이 가져오려고 한다면,

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%203.png)

우선 member 쿼리가 하나 나간다.

이건

`List<Member> members = memberRepository.findAll();` 이 시점에 나가는 쿼리다

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%204.png)

그 뒤에 team을 DB에서 가져온다.

보면 member1가져오고 teamA가져오는 쿼리가 나가고,

member2가져오고 teamB 가져오는 쿼리가 나간다.

**이게 Team에 대해 가짜 객체(Hibernate.Proxy)를 가져오는 LAZY Fetch 조인 때문에 벌어진 일이다.**

**Team에 대해서 name을 가져오라는 실제적인 문제가 생긴거죠**

**member1에 대해서 proxy 객체만 있으니까 첫 루프에서 teamA 불러오라고하면 조회 쿼리가 날아가야한다. 똑같이 member2에 대해서 proxy 객체만 있으니까 첫 루프에서 teamB 불러오라고하면 조회 쿼리가 날아가야한다. (N + 1)**

### 참고: 다음과 같이 지연 로딩 여부를 확인할 수 있다.

```java
//Hibernate 기능으로 확인
Hibernate.isInitialized(member.getTeam())

//JPA 표준 방법으로 확인
PersistenceUnitUtil util =
em.getEntityManagerFactory().getPersistenceUnitUtil();
util.isLoaded(member.getTeam());
```

쿼리가 10번 더 나가면 네트워크를 10번 더 타는거에요

## JPA는 이를 Fetch JOIN으로 해결

`MemberRepository.java`

```java
@Query("select m from Member m left join fetch m.team")
List<Member> findMemberFetchJoin();
```

`fetch [m.team](http://m.team)` 을 추가한다 ⇒ 특이한 옵션인데,

**fetch 조인을 하면 Member를 조회할때 연관된 Team을 같이 한방에 가져온다.**

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%205.png)

`findMemberFetchJoin();` 으로 바꾸어 실행해보자.

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%206.png)

한번에 다 끌고와서,

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%207.png)

Member객체의 id, username, age뿐만 아니라
Team 엔티티까지 생성해서 넣어놓는다.

iter문에 `System.*out*.println("member.teamClass = " + member.getTeam().getClass());` 를 추가해서 확인해보자

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%208.png)

프록시가 아니라 진짜 entity가 생성되어 나온다.

## Fetch 조인이란

연관관계가 있는 것을 JOIN을 이용해 (객체 그래프) DB에서 한번에 끌고오는 것DB 조인은 조인만 하는데, Fetch 조인은 JOIN하고, SELECT절에 그 데이터를 다 넣어준다.

스프링 데이터 JPA는 JPA가 제공하는 엔티티 그래프 기능을 편리하게 사용하게 도와준다. 이 기능을 사용하면 JPQL 없이 페치 조인을 사용할 수 있다. (JPQL + 엔티티 그래프도 가능)

## EntityGraph란?

`MemberRepository.java`

findAll(); 메소드를 오버라이딩 해보자.

```java
@Override
@EntityGraph(attributePaths = {"team"})
List<Member> findAll();
```

findAll 할때 멤버도 조회하면서 Team도 조회하고 싶으면 JPQL을 짜야하는데, 그게 귀찮을 때 EntityGraph를 사용할 수 있다.

`attributePaths =` 옵션을 “team” 으로 적어주면 된다.

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%209.png)

다시 findAll();로 바꾸어 실행시키면, 

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%2010.png)

아까 fetch 조인한 것과 같은 결과가 나온다.

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%2011.png)

사실 내부적으로는 fetch 조인을 사용하는 것과 같다.

### EntityGraph 정리

- 사실상 페치 조인(FETCH JOIN)의 간편 버전
- LEFT OUTER JOIN 사용

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%2012.png)

이렇게 쿼리를 짠 후에 EntityGraph를 추가해주는 사용법도 가능하다.

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%2013.png)

이렇게도 사용이 가능하구요

### NamedEntityGraph 사용 방법

`@NamedEntityGraph(name = "Member.all", attributeNodes = @NamedAttributeNode("team"))`

를 public class Member{} 위에 적어주면, 

![Untitled](@EntityGraph%206a565bcfb35a40f887fed43afd1e6aa1/Untitled%2014.png)

처럼 사용 가능하다

EntityGraph는 간단간단한걸 하는데 굳이 JPQL 사용하기 귀찮을때 사용하는거라고 보면 됨