# 영속성 전이(CASCADE)와 고아 객체

## 영속성 전이: CASCADE

- **특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속상태로 만들고 싶을 때**
- **예: 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장.**

parent child 있었다면

```java
try {
    Child child1 = new Child();
    Child child2 = new Child();

    Parent parent = new Parent();
    parent.addChild(child1);
    parent.addChild(child2);

    em.persist(parent);
    em.persist(child1);
    em.persist(child2);

    tx.commit();
}
```

과 같이 persist를 여러번 써주어야 한다.

## 근데 나는 코드 짤때 parent 중심으로 짜고싶어..

## parent 중심으로 짜면 child가 알아서 따라왔으면 좋겠어.

→ `cascade = CascadeType.ALL` 옵션 써봐!

```java
try {
    Child child1 = new Child();
    Child child2 = new Child();

    Parent parent = new Parent();
    parent.addChild(child1);
    parent.addChild(child2);

    em.persist(parent);

    tx.commit();
}
```

⇒ 이렇게만해도 child 둘다 잘 `persist`되어있다!

## 영속성 전이: CASCADE - 주의!

- 영속성 전이는 연관관계를 매핑하는 것과 아무 관련이 없음
- 엔티티를 영속화할 때 연관된 엔티티도 함께 영속화하는 편리함을 제공할 뿐
    
    **파일을 다른 엔티티에서 관리하면 쓰면 안된다…!!(소유자가 하나일때만 사용해야해요)**
    

## CASCADE의 종류

- **ALL: 모두 적용**
- **PERSIST: 영속    ← 요 두개를 많이쓴다.!**
- REMOVE: 삭제
- MERGE: 병합
- REFRESH: REFRESH
- DETACH: DETACH

### 언제 많이 쓰나요?

**→ 하나의 부모가 자식들을 관리할때 의미가 있다**
ex) 게시판 - 첨부파일(테이블 데이터)

## 고아 객체

**고아 객체:  부모 엔티티와 연관관계가 끊어진 자식 엔티티**

`orphanRemoval = true` ← 이 옵션: 엔티티 끊어지면 지워진다!

`Parent parent1 = em.find(Parent.class, id);
parent1.getChildren().remove(0);`
→ 자식 엔티티를 컬렉션에서 제거 

`DELETE FROM CHILD WHERE ID=?` ← DELETE 쿼리가 나온다!

## 고아 객체 - 주의

- **참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 보고 삭제하는 기능**
- **참조하는 곳이 하나일 때 사용해야 함!
→ @OneToOne, @OneToMany만 가능**
- **특정 엔티티가 개인 소유할 때 사용**
- 참고: **개념적으로 부모를 제거하면 자식은 고아가 된다**. 따라서 고
아 객체 제거 기능을 활성화 하면, **부모를 제거할 때 자식도 함께
제거된다**. 이것은 **CascadeType.REMOVE처럼 동작한다**.

## 영속성 전이 + 고아 객체, 생명주기

**CascadeType.ALL + orphanRemovel=true (둘 다 켜기)**

- 스스로 생명주기를 관리하는 엔티티는 **`em.persist()`**로 영속화, `em.remove()`로 제거
- **두 옵션을 모두 활성화 하면 부모 엔티티를 통해서 자식의 생명주기를 관리할 수 있음
→ `em.persist(parent), em.remove(parent)` 만 했는데도 자식 엔티티를 조종할 수 있다. 생명주기를 부모에 맞출 수 있다.**
- **도메인 주도 설계(DDD**)의 `Aggregate Root개념을 구현할 때 유용`