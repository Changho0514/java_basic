# 주문 조회 V4: JPA에서 DTO 직접 조회

## **Collection이 있을때 DTO를 이용하여 직접 조회하는 법**

`[jpabook.jpashop.repository.query.OrderQueryRepository.java](http://jpabook.jpashop.repository.query.OrderQueryRepository.java)` 추가

`OrderRepository`는 Order Entity 조회 등의 용도로 사용. 

`OrderQueryRepository` **는 화면이나 API과 의존관계가 있는 쪽을 떼어내려 사용. 핵심. 엔티티 조회가 아닌 특정 화면에 fit한 쿼리들**.

장점 → 화면과 관련된 것들은 생각보다 쿼리와 밀접하게 연관된 경우가 많다. 중요한 핵심 비즈니스 로직들은 OrderRepository 이용하면 됩니다.

<aside>
✅ **라이프 싸이클이 생각보다 다르다.
화면-API는 비슷하지만,
핵심비즈니스 처리하기위한 라이프싸이클은 다른것과 다르다.**

</aside>

`OrderQueryRepository`로 분리시켜주려하고 있으므로, 

`[OrderApiController.java](http://OrderApiController.java)` 에 

`private final OrderQueryRepository orderQueryRepository;` 를 추가해주고,

### 꿀팁!

> `**intelliJ` 에서 `F2` 를 누르면 다음 에러로 갑니다**
> 

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V4%20JPA%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20DTO%20%E1%84%8C%E1%85%B5%E1%86%A8%E1%84%8C%E1%85%A5%E1%86%B8%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%204819e995bc2a412db35a8897323a1c4d/Untitled.png)

`OrderQueryDto`를 만들어주어야겠네요.

`[repository.order.query.OrderQueryDto.java](http://repository.order.query.OrderQueryDto.java)` 만들어주기

```java
package jpabook.jpashop.repository.order.query;

import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;

import java.time.LocalDateTime;
import java.util.List;

@Data
public class OrderQueryDto {

    private Long id;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemQueryDto> orderItems;
}
```

`[repository.order.query.](http://repository.order.query.OrderQueryDto.java)OrderItemQueryDto.java`

```java
package jpabook.jpashop.repository.order.query;

import lombok.Data;

@Data
public class OrderItemQueryDto {

    private String itemName;
    private int orderPrice;
    private int count;
}

```

그리고선,

`[OrderQueryDto.java](http://OrderQueryDto.java)` 에 Constructor를 추가해주고,

```java
package jpabook.jpashop.repository.order.query;

import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;

import java.time.LocalDateTime;
import java.util.List;

@Data
public class OrderQueryDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemQueryDto> orderItems;

    public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address, List<OrderItemQueryDto> orderItems) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
        this.orderItems = orderItems;
    }
}

```

### `Q. 왜 [OrderApiContorller.java](http://OrderApiContorller.java)` 에 직접 `OrderQueryDto`를 만들지 않았나요?

→ OrderApiController 안에 있는 `OrderDto`를 참조한다면 repository가 Controller를 참조하는 순환관계가 생겨버립니다.
그리고, `OrderQueryRepository`가  `OrderQueryDto`를 만드니 같은 패키지 안에 있는것이 좋겠습니다

쿼리를 짜서 바로 DTO로 만들면 좋겠죠?

`[OrderQueryRepository.java](http://OrderQueryRepository.java)` 에 추가

```java
public List<OrderQueryDto> findOrderQueryDtos() {
        return em.createQuery(
                "select new jpabook.jpashop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate,o.status, d.address" +
                        " join o.member m" +
                        " join o.delivery d", OrderQueryDto.class)
                .getResultList();
    }
```

`[OrderQueryDto.java](http://OrderQueryDto.java)` 생성자에서는 

잠시 `List<OrderItemQueryDto> orderItems` 를 빼준다.

왜냐하면 new operation에서 문제가 JPQL을 위와 같이 짜더라도 

Collection을 바로 넣을 순 없어요.

SQL을 생각하면 되는데, new operation쓸때 SQL처럼 사용하는 것인데, 거기에 Dto로 만드는 것 뿐이에요. 그래서 데이터를 플랫하게 한 줄로 밖에 못 넣습니다.

OrderItems는 일대다니까 바로 플랫하게 넣지 못한다.
데이터가 뻠핑이 되어있기 때문에.

그래서
`OrderQueryRepository.java`의

```java
return em.createQuery(
        "select new jpabook.jpashop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate,o.status, d.address" +
								" from Order o" +
                " join o.member m" +
                " join o.delivery d", OrderQueryDto.class)
        .getResultList();
```

이 부분을 `command + option + m` 버튼으로 `extract method` 해서 `findOrders()`로 만들고,

루프로 돌려서 Collection을 가져와야 한다.

```java
public List<OrderQueryDto> findOrderQueryDtos() {
    List<OrderQueryDto> result = findOrders();

    result.forEach(o -> {
        List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
o.setOrderItems(orderItems);
    });
}
```

이렇게 만들어주고,

**일대 다 이기때문에 ‘다' 부분을 해결할 수 없어서 쿼리를 다시 따로 짜주어야 한다.**

```java
private List<OrderItemQueryDto> findOrderItems(Long orderId) {
        return em.createQuery(
                        "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name, oi.orderPrice, oi.count)" +
                                " from OrderItem oi" +
                                " join oi.item i" +
                                " where oi.order.id = :orderId", OrderItemQueryDto.class)
                .setParameter("orderId", orderId)
                .getResultList();
    }
```

`OrderQueryRepository.java`

```java
package jpabook.jpashop.repository.order.query;

import jpabook.jpashop.domain.OrderItem;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import java.util.List;

@Repository
@RequiredArgsConstructor
public class OrderQueryRepository {

    private final EntityManager em;

/**
     *컬렉션은 별도로 조회
* Query:루트1번,컬렉션N번*단건 조회에서 많이 사용하는 방식
*/

public List<OrderQueryDto> findOrderQueryDtos() {
        //루트 조회(toOne 코드를 모두 한번에 조회)
        List<OrderQueryDto> result = findOrders();

        //루프를 돌면서 컬렉션 추가(추가 쿼리 실행)
        result.forEach(o -> {
            List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
            o.setOrderItems(orderItems);
        });
        return result;
    }

/**
     * 1:N관계인orderItems조회
*/
private List<OrderItemQueryDto> findOrderItems(Long orderId) {
        return em.createQuery(
                        "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name, oi.orderPrice, oi.count)" +
                                " from OrderItem oi" +
                                " join oi.item i" +
                                " where oi.order.id = :orderId", OrderItemQueryDto.class)
                .setParameter("orderId", orderId)
                .getResultList();
    }

/**
     * 1:N관계(컬렉션)를 제외한 나머지를 한번에 조회
*/
private List<OrderQueryDto> findOrders() {
        return em.createQuery(
                        "select new jpabook.jpashop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate, o.status, d.address)" +
                                " from Order o" +
                                " join o.member m" +
                                " join o.delivery d", OrderQueryDto.class)
                .getResultList();
    }
}

```

실행시켜보면,

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V4%20JPA%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20DTO%20%E1%84%8C%E1%85%B5%E1%86%A8%E1%84%8C%E1%85%A5%E1%86%B8%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%204819e995bc2a412db35a8897323a1c4d/Untitled%201.png)

원하는대로 잘 뜬다.

- Query: 루트 1번, 컬렉션 N 번 실행
ToOne(N:1, 1:1) 관계들을 먼저 조회하고, ToMany(1:N) 관계는 각각 별도로 처리한다.

이런 방식을 선택한 이유는 다음과 같다.
**`ToOne 관계는 조인해도 데이터 row 수가 증가하지 않는다.
ToMany(1:N) 관계는 조인하면 row 수가 증가한다.`
그래서 함부로 조인해서 가져올 수 없다.**

row 수가 증가하지 않는 ToOne 관계는 조인으로 최적화 하기 쉬우므로 한번에 조회하고, ToMany관계는 최적화 하기 어려우므로 `findOrderItems()` 같은 별도의 메서드로 조회한다.

이렇게하면 쿼리가 2번 나간다.

쿼리는 총 3번이 나간다.
결과적으로는 N+1이다.

```java
public List<OrderQueryDto> findOrderQueryDtos() {
    //루트 조회(toOne 코드를 모두 한번에 조회)
    List<OrderQueryDto> result = findOrders();

    //루프를 돌면서 컬렉션 추가(추가 쿼리 실행)
    result.forEach(o -> {
        List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
        o.setOrderItems(orderItems);
    });
    return result;
}
```

    `List<OrderQueryDto> result = findOrders();`

→ 여기서 1번 → 2개가 나옴.

이것때문에

`List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());` 이 쿼리도 2번 실행.

**결과적으로 N+1번**