# 시작 - JPQL vs Querydsl

Test를 먼저 만들어보자.

`study.querydsl.QuerydslBasicTest.java`

`@BeforeEach` -> 각 테스트 실행전에 데이터를 세팅하기 위함.

```java
@BeforeEach
public void before() {
    Team teamA = new Team("teamA");
    Team teamB = new Team("teamB");
    em.persist(teamA);
    em.persist(teamB);

    Member member1 = new Member("member1", 10, teamA);
    Member member2 = new Member("member2", 20, teamA);

    Member member3 = new Member("member3", 30, teamB);
    Member member4 = new Member("member4", 40, teamB);

    em.persist(member1);
    em.persist(member2);
    em.persist(member3);
    em.persist(member4);
}
```

### 꿀팁! query String이 너무 길때

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled.png)

`Command + option + V`를 누르면 extract 할 수 있다.

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%201.png)

`Member m`  뒤에서 엔터를 누르면 알아서 +로 바꿔준다

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%202.png)

`Querydsl`은 `JPAQueryFactory`라는 것으로 시작해야한다.

QMember라는게 있어야하는데 없다 왜? ⇒ 엔티티만 만들고 해준게 없잖아요.

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%203.png)

Gradle의 `compileQuerydsl` 을 더블클릭해주자

그럼

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%204.png)

이 글이 뜨면서

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%205.png)

`build → generated → querydsl`에 `QMember, QTeam`이 생겼다

이제 `QMember`를 쓸 수 있다.

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%206.png)

`variable`을 줘야한다 ⇒ 어떤 `QMember`인지를 구분하는 장치.

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%207.png)

- JPAQueryFactory를 만들때 `entityManager`를 생성자에 넘겨줘야한다.
`EntityManager`를 갖고 데이터를 찾던가 해야하기 때문에
- `QMember`는 변수명에 별칭과 같이 줘야한다. 구분해야 하기에 
하지만 나중에 `QMember.member` 과 같이 쓸것이기 때문에 크게 중요하진 않다.
- JPQL은 파라미터 바인딩(`.setParameter`)을 해줘야 하는데, `Querydsl`은 필요없다. 자동으로 SQL의 JDBC의 prepare Statement로 파라미터 바인딩을 자동으로 해준다.

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%208.png)

`username`이 `?`로 나갔다.

⇒ 자동으로  prepare Statement의 파라미터 바인딩 방식을 사용하는 것.

그러면 DB입장, 성능 면에서 유리하다

- **그리고, JPQL은 오타가 만약 나면, 실행해서 메서드를 호출해야 확인 가능.
Runtime 오류 ⇒ 최악의 오류**

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%209.png)

- Querydsl은 오류를 냈으면 **`Compile 오류를 냄` ⇒ Q타입 파일 생성하는 이유**
- Java 컴파일러의 도움을 받는다.

## 코드를 조금씩 줄여보자

`JPAQueryFactory queryFactory = new JPAQueryFactory(em);`

이 친구는 빼내서 필드로 가져가도 된다.

**Repository 작성할때도 써도 된다.**

![Untitled](%E1%84%89%E1%85%B5%E1%84%8C%E1%85%A1%E1%86%A8%20-%20JPQL%20vs%20Querydsl%20c1b06af3ad6040fdb2ae0adab7e20d1c/Untitled%2010.png)

근데 이렇게 하면

동시성 문제는 없나요? 멀티 쓰레드에서  EntityManager 접근하면 어떻게 될까요

현재 내 Transaction이 어디에 걸려있는지에 따라 
Transaction에 바인딩 해준다. ⇒ 멀티 쓰레드에 관계없이 사용 가능