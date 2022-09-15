# 프로젝션과 결과 반환 - DTO 조회

### 순수 JPA에서 DTO 조회

`study.querydsl.dto.MemberDto.java`

```java
@Data
public class MemberDto {

    private String username;
    private int age;

    public MemberDto(String username, int age) {
        this.username = username;
        this.age = age;
    }
}
```

DTO - **엔티티를 조회하는 것이 아니라(Member의 정보를 다 가져오는), username, age만 가져오고싶다. 데이터를 담는 통이라고 생각하면 된다.**

### **JPQL로 DTO 사용하기**

```java
@Test
public void findDtoByJPQL() {
    em.createQuery("select m from Member m", MemberDto.class)
}
```

이렇게 쓰면 select로 가져온 타입이 `Member` 이므로 `MemberDto.class`와 맞춰지지 않는다.

`select m.username, m.age` 이렇게 쓰는 것도 맞지 않다.

### New Operation을 써야한다.

`em.createQuery("select new study.querydsl.dto.MemberDto(m.username, m.age) from Member m", MemberDto.class)`

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled.png)

마치 생성자를 쓰는 것 처럼 써줘야한다.

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%201.png)

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%202.png)

잘 나오지만, 참 별로다. 언제 패키지 명을 다 적고 있나.

- **순수 JPA에서 DTO를 조회할 때는 new 명령어를 사용해야함**
- **DTO의 package이름을 다 적어줘야해서 지저분함**
- **생성자 방식만 지원함**

## Querydsl 빈 생성(Bean population)

### **결과를 DTO 반환할 때 사용
다음 3가지 방법 지원**

- 프로퍼티 접근
- 필드 직접 접근
- 생성자 사용

### 1. 프로퍼티 접근 - Setter를 사용하는 방법.

```java
@Test
public void findDtoBySetter() {
    List<MemberDto> result = queryFactory
            .select(Projections.bean(MemberDto.class,
										member.username,
										member.age))
            .from(member)
            .fetch();

		for (MemberDto memberDto : result) {
            System.out.println("memberDto = " + memberDto);
        }
}
```

이대로 돌리면 에러가 뜨는데,

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%203.png)

기본 생성자를 만들어주지 않아서 그렇다.

→ **@NoArgsConstructor** 를 `MemberDto`에 추가해주자.

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%204.png)

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%205.png)

Setter를 통해서 값이 생성되었다.

### 2. 필드 직접 접근

```java
@Test
public void findDtoByField() {
    List<MemberDto> result = queryFactory
            .select(Projections.fields(MemberDto.class,
member.username,
member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}
```

`.fields` 로 바꾸기만 하고 돌리면 된다.

**Getter, Setter 필요없이 필드에 바로 값을 쫙 넣어버린다.**

### 3. 생성자 사용 방법

```java
@Test
public void findDtoByConstructor() {
    List<MemberDto> result = queryFactory
            .select(Projections.constructor(MemberDto.class,
member.username,
member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}
```

## 별칭이 다를 때

`UserDto.java`

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%206.png)

`private String username;` 이라고 했었는데

`private String name;` 로 바꿨다

![Untitled](%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%20%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20DTO%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%2000a56199a0fd4861bcec77d81c44f468/Untitled%207.png)

`MemberDto`를 사용하다가 `UserDto`를 사용하게 된 상황.
근데 username으로 쓰던게 이젠 name으로 써야 한다.

`.fields`  → 필드로 하기때문에 필드 명이 맞아야 한다.

해결방안 → *`member*.username.as("name"),` 으로 바꿔준다.

age로 서브쿼리 사용할 경우 ⇒ 별칭을 또 지어줘야한다.

```java
ExpressionUtils.as(
        JPAExpressions
                .select(memberSub.age.max())
                .from(memberSub), "age")
)
```

회원 나이의 최대 나이로 다 맞춰버리고 싶다. ⇒ 40살로 다 맞춰버리기

- 프로퍼티나, 필드 접근 생성 방식에서 이름이 다를 때 해결 방안
- [`ExpressionUtils.as](http://expressionutils.as/)(source,alias)` : **필드나, 서브 쿼리에 별칭 적용**
[`username.as](http://username.as/)("memberName")` : **필드에 별칭 적용**

### 4. 생성자 사용

```java
List<MemberDto> result = queryFactory
				.select(Projections.constructor(MemberDto.class,
								member.username,
								member.age))
				.from(member)
				.fetch();
}
```