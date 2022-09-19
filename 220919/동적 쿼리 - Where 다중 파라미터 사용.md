# 동적 쿼리 - Where 다중 파라미터 사용

### 실무에서 굉장히 좋아하는 방법

코드가 깔끔하게 나온다.

![Untitled](%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%20%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20-%20Where%20%E1%84%83%E1%85%A1%E1%84%8C%E1%85%AE%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20731014343e2748b59ec69cb75c56ee4d/Untitled.png)

where문에 `usernameEq, ageEq` 메서드를 생성해주자

![Untitled](%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%20%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20-%20Where%20%E1%84%83%E1%85%A1%E1%84%8C%E1%85%AE%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20731014343e2748b59ec69cb75c56ee4d/Untitled%201.png)

이러면 `usernameCond`에 null이 들어왔을 경우에는 아래와 같다.

**parameter가 여러개 나열되면 and 조건이기 때문에.**

**null이 들어오면 그냥 무시됩니다. ⇒ 동적 쿼리가 가능해진다.**

![Untitled](%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%20%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20-%20Where%20%E1%84%83%E1%85%A1%E1%84%8C%E1%85%AE%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20731014343e2748b59ec69cb75c56ee4d/Untitled%202.png)

## 삼항 연산자 사용하기

![Untitled](%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%20%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20-%20Where%20%E1%84%83%E1%85%A1%E1%84%8C%E1%85%AE%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20731014343e2748b59ec69cb75c56ee4d/Untitled%203.png)

if에 커서를 두고 `option + enter` 

⇒

![Untitled](%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%20%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20-%20Where%20%E1%84%83%E1%85%A1%E1%84%8C%E1%85%AE%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20731014343e2748b59ec69cb75c56ee4d/Untitled%204.png)

### 근데 메서드 만드는 게 더 귀찮지 않아?

```java
private List<Member> searchMember2(String usernameCond, Integer ageCond) {
    return queryFactory
            .selectFrom(member)
            .where(usernameEq(usernameCond), ageEq(ageCond))
            .fetch();
}
```

우리가 개발할 때는 이걸 더 많이 봐요. 메서드 하나보다는.

그래서 그냥 건너가는게 편하죠

**또 메서드로 빠졌잖아요**

⇒ 조립이 가능해집니다.

```java
private BooleanExpression allEq(String usernameCond, Integer ageCond) {
    return usernameEq(usernameCond).and(ageEq(ageCond));
}
```

### 실무에서 예

광고 상태 `isValid` 날짜가 IN: `isServicable` 

```java
private BooleanExpression isServicable(String usernameCond, Integer ageCond) {
         return isValid(usernameCond).and(DateBetweenIn(ageCond));
}
```

![Untitled](%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%20%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20-%20Where%20%E1%84%83%E1%85%A1%E1%84%8C%E1%85%AE%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20731014343e2748b59ec69cb75c56ee4d/Untitled%205.png)

**이런식으로 들어갈 수 있고, 계속 다른 곳에서도 사용이 가능하다는 점!**

### 장점

- where 조건에 null 값은 무시된다.
- **메서드를 다른 쿼리에서도 재활용 할 수 있다.**
- **쿼리 자체의 가독성이 높아진다.**