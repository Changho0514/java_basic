# 주문 조회 V3:엔티티를 DTO로 변환 - 페치 조인 최적화

`[OrderRepository.java](http://OrderRepository.java)` 에 `findAllWithItem` 추가

```java
public List<Order> findAllWithItem() {
    return em.createQuery(
                    "select distinct o from Order o" +
                            " join fetch o.member m" +
                            " join fetch o.delivery d" +
                            " join fetch o.orderItems oi" +
                            " join fetch oi.item i", Order.class)
            .getResultList();
}
```

문제는 현재 order 2개, orderItem 4개인데, 
fetch Join 하면 Order가 4개가 된다.

DB 입장에서는 

쿼리가 
`select * from orders o
join order_item oi on o.order_id =oi.order_id;` 로 나간다.

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled.png)

**ORDER_ID가 4번이 2개, 11번이 2개가 되어버린다**

ORDER_ITEM 갯수에 맞추어 ORDER가 늘어나버린다.
→ JPA에서 ORDER 가져올때 데이터가 2배가 되어버린다.

```java
@GetMapping("/api/v3/orders")
public List<OrderDto> ordersV3() {
    List<Order> orders = orderRepository.findAllWithItem();
    List<OrderDto> result = orders.stream()
            .map(o -> new OrderDto(o))
            .collect(toList());
    return result;
}
```

다른 코드는 같다

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%201.png)

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%202.png)

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%203.png)

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%204.png)

이렇게 주문이 4개가 되어버린다!

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%205.png)

`ItelliJ`에서 쿼리를 복사해서 그대로 H2 콘솔에서 실행해보자

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%206.png)

**Data row가 4개가 된다.**

Fetch JOIN 도 DB, SQL 입장에서는 그냥 JOIN이에요
대신 SELECT 절에 더 DATA를 넣냐 마냐 정도.

이를 객체그래프로 담아야하는데 DB입장에서 뻥튀기 된 것을 hibernate 등의 입장에서는 뻥튀기 된 그대로 써야하는지 모른다.

### 우리가 원하는것

→ `order`는 뻥튀기하지 말아주세요!

그리고 사실 order는 복사된 값일 뿐이다.

확인해보기 위해 order의 id값과 참조값을 출력해보자

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%207.png)

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%208.png)

## 해석

→ order가 PK Id, 참조값까지 똑같다.

## 해결방법

`distinct`를 넣어준다.

`"select distinct o from Order o"` 

중복을 제거해준다.

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%209.png)

이 친구는 실제로 쿼리에도 `distinct` 를 넣어서 조회한다.

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%2010.png)

⇒ **iteration도 2번만 실행된다!**

**하지만, H2 콘솔에서 실행시키면** 

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%2011.png)

4줄이 나온다.

### DB는 한 줄이 전부 같은 값이어야 중복을 제거해준다!

즉, db쿼리에서는 상황이 다르다.
db 쿼리도 distinct가 존재하지만, JPA의 distinct와 조금 다르다.

JPA에서는 distinct가 있을때 가져오면,
Order가 같은 id값을 가지면 중복을 제거해서 가져온다.

## 정리

- **페치 조인으로 SQL이 1번만 실행됨**
- 코드는 같다.

```java
@GetMapping("/api/v2/orders")
public List<OrderDto> ordersV2() {
    List<Order> orders = orderRepository.findAll();
    List<OrderDto> result = orders.stream()
            .map(o -> new OrderDto(o))
            .collect(toList());
    return result;
}
```

```java
@GetMapping("/api/v3/orders")
public List<OrderDto> ordersV3() {
    List<Order> orders = orderRepository.findAllWithItem();
    List<OrderDto> result = orders.stream()
            .map(o -> new OrderDto(o))
            .collect(toList());
    return result;
}
```

- **이게 어마어마한 장점이다!
실무에서 같은 코드를 사용하는데도,
Fetch 조인으로 손쉽게 바꿀수있다
→ 성능이 어마어마하게 좋아진다!**
- distinct 를 사용한 이유는 **1대다 조인이 있으므로 데이터베이스 row가 증가한다**. 그 결과 같은 order 엔티티의 조회 수도 증가하게 된다. JPA의 distinct는 SQL에 distinct를 추가하고, **더해서 같은 엔티티가 조회되면, 애플리케이션에서 중복을 걸러준다**. 이 예에서 order가 컬렉션 페치 조인 때문에 중복 조회 되는 것을 막아준다.
- 단점
    - **`페이징 불가능`(굉장한 단점!!)**
    
    ![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%2012.png)
    
    페이징을 실행해보면
    

```java
select
        distinct order0_.order_id as order_id1_6_0_,
        member1_.member_id as member_i1_4_1_,
        delivery2_.delivery_id as delivery1_2_2_,
        orderitems3_.order_item_id as order_it1_5_3_,
        item4_.item_id as item_id2_3_4_,
        order0_.delivery_id as delivery4_6_0_,
        order0_.member_id as member_i5_6_0_,
        order0_.order_date as order_da2_6_0_,
        order0_.status as status3_6_0_,
        member1_.city as city2_4_1_,
        member1_.street as street3_4_1_,
        member1_.zipcode as zipcode4_4_1_,
        member1_.name as name5_4_1_,
        delivery2_.city as city2_2_2_,
        delivery2_.street as street3_2_2_,
        delivery2_.zipcode as zipcode4_2_2_,
        delivery2_.status as status5_2_2_,
        orderitems3_.count as count2_5_3_,
        orderitems3_.item_id as item_id4_5_3_,
        orderitems3_.order_id as order_id5_5_3_,
        orderitems3_.order_price as order_pr3_5_3_,
        orderitems3_.order_id as order_id5_5_0__,
        orderitems3_.order_item_id as order_it1_5_0__,
        item4_.name as name3_3_4_,
        item4_.price as price4_3_4_,
        item4_.stock_quantity as stock_qu5_3_4_,
        item4_.artist as artist6_3_4_,
        item4_.etc as etc7_3_4_,
        item4_.author as author8_3_4_,
        item4_.isbn as isbn9_3_4_,
        item4_.actor as actor10_3_4_,
        item4_.director as directo11_3_4_,
        item4_.dtype as dtype1_3_4_ 
    from
        orders order0_ 
    inner join
        member member1_ 
            on order0_.member_id=member1_.member_id 
    inner join
        delivery delivery2_ 
            on order0_.delivery_id=delivery2_.delivery_id 
    inner join
        order_item orderitems3_ 
            on order0_.order_id=orderitems3_.order_id 
    inner join
        item item4_ 
            on orderitems3_.item_id=item4_.item_id
```

- **limit, Offset 하나도 없음**
- warning 로그를 보면

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%2013.png)

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V3%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20-%20%E1%84%91%E1%85%A6%E1%84%8E%E1%85%B5%20%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%B5%E1%86%AB%20fa6475b301f647b38b372adfa76f4e62/Untitled%2014.png)

**firstResult/maxResults 가 collection fetch 조인과 같이 정의가 되었다는게 문제임! → “나는 메모리에서 이거 페이징할거야!” “경고야!”**

- **일대다 조인 하는 순간 order의 기준 자체가 틀어져버린다
그래서 중간부터 갯수를 맞출 수 없으니
hibernate에서는 에러를 내버리고
메모리에서 해보겠다는것 ← 매우 위험.**
- 일대다관계가 아니라면 괜찮다

> • 참고: 컬렉션 페치 조인을 사용하면 페이징이 불가능하다. 하이버네이트는 경고 로그를 남기면서 **모든 데이터를 DB에서 읽어오고, 메모리에서 페이징 해버린다(매우 위험하다).** 자세한 내용은 자바 ORM 표준 JPA 프로그래밍의 페치 조인 부분을 참고하자.
> 

> •   참고: 컬렉션 페치 조인은 1개만 사용할 수 있다. 컬렉션 둘 이상에 페치 조인을 사용하면 안된다. **데이터가 부정합하게 조회될 수 있다(N*N이 될수도 있다)** 자세한 내용은 자바 ORM 표준 JPA 프로그래밍을 참고하자.
>