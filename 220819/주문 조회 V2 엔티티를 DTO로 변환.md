# 주문 조회 V2: 엔티티를 DTO로 변환

```java
package jpabook.jpashop.api;

import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderItem;
import jpabook.jpashop.domain.OrderStatus;
import jpabook.jpashop.repository.OrderRepository;
import jpabook.jpashop.repository.OrderSearch;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDateTime;
import java.util.List;
import java.util.stream.Collectors;

@RestController
@RequiredArgsConstructor
public class OrderApiController {

    private final OrderRepository orderRepository;

/**
     * V1.엔티티 직접 노출
* - Hibernate5Module모듈 등록, LAZY=null처리* -양방향 관계 문제 발생-> @JsonIgnore
     */
@GetMapping("/api/v1/orders")
    public List<Order> ordersV1() {
        List<Order> all = orderRepository.findAllByString(new OrderSearch());
        for (Order order : all) {
            order.getMember().getName(); //Lazy 강제 초기화
            order.getDelivery().getAddress(); //Lazy 강제 초기화

            List<OrderItem> orderItems = order.getOrderItems();
            orderItems.stream().forEach(o -> o.getItem().getName()); //Lazy 강제 초기화
        }
        return all;
    }

    @GetMapping("/api/v2/orders")
    public List<OrderDto> ordersV2() {
        List<Order> orders = orderRepository.findAllByString(new OrderSearch());
        List<OrderDto> collect = orders.stream()
                .map(o -> new OrderDto(o))
                .collect(Collectors.toList());
        return collect;
    }

    static class OrderDto {

        //DTO가 반환됩니다.
        private Long orderId;
        private String name;
        private LocalDateTime orderDate;
        private OrderStatus orderStatus;
        private Address address;
        private List<OrderItem> orderItems;

        public OrderDto(Order order) {
            orderId = order.getId();
            name = order.getMember().getName();
            orderDate = order.getOrderDate();
            orderStatus = order.getStatus();
            address = order.getDelivery().getAddress();
            orderItems = order.getOrderItems();
        }
    }

}

```

돌리고 문제점을 찾는다면, 

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V2%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20cf510e8c9a114469855b7a24609bd4ea/Untitled.png)

**No properties가 뜨는데,**

대부분 properties는 **@Getter, @Setter** 를 말한다

@Getter를 `static class OrderDto` 위에 추가 후, 다시 돌려보면

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V2%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20cf510e8c9a114469855b7a24609bd4ea/Untitled%201.png)

응? `OrderItems`가 안나오네?

생각해보니 `OrderItems`가 엔티티였기 때문이다!!

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V2%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20cf510e8c9a114469855b7a24609bd4ea/Untitled%202.png)

프록시 초기화를 넣어서 다시 돌려보자

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V2%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20cf510e8c9a114469855b7a24609bd4ea/Untitled%203.png)

잘 나온다 

하지만 문제가 있다!
”DTO를 반환해” 라고 했으면 DTO 안에 엔티티가 있으면 안된다. 랩핑하는것조차 안된다. → 엔티티가 노출된다

```java
order.getOrderItems().stream().forEach(o -> o.getItem());
orderItems = order.getOrderItems();
```

이렇게 `orderItems` 엔티티를 반환하면 안된다.

**이것조차도 DTO로 바꿔야한다.**

```java
orderItems = order.getOrderItems().stream()
                .map(orderItem -> new OrderItemDto(orderItem))
                .collect(Collectors.toList());
    }
}

@Getter
static class OrderItemDto() {
    private String itemName;
    private int orderPrice;
    private int count;

    public OrderItemDto(OrderItem orderItem) {
        itemName = orderItem.getItem().getName();
        orderPrice = orderItem.getOrderPrice();
        count = orderItem.getCount();
```

외부로는 `OrderDto에서 OrderItemDto`로 래핑해서 나가기때문에

이렇게 해야 엔티티를 출력하지 않고 온전히 DTO로만 놀 수 있다.

돌려본다면 

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V2%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20cf510e8c9a114469855b7a24609bd4ea/Untitled%204.png)

`orderItems` 에 궁금한 데이터가 깔끔하게 나온다.

## 중요

엔티티를 바깥으로 노출하지 말라는건 
단순히 껍데기만 노출하지 말라는것이 아니라
그 속에 있는 것까지 모두 다 엔티티를 바깥으로 노출하면 안된다.(value값은 괜찮음)

## 정리

![Untitled](%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V2%20%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%90%E1%85%B5%E1%84%90%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A7%E1%86%AB%E1%84%92%E1%85%AA%E1%86%AB%20cf510e8c9a114469855b7a24609bd4ea/Untitled%205.png)

1. `orders`가 컬렉션(엔티티)이므로 stream으로 변경하여
DTO로 변환시켜준다.
2. 생성자로 넘기는 파라미터 값에서 또 엔티티가 있다면(orderItems)
또 DTO로 변환시켜준다.

## 평가

- 지연 로딩으로 너무 많은 SQL 실행
- SQL 실행 수
    - **order 1번**
    - **member , address N번(order 조회 수 만큼)
    (여기선 주문이 2개이므로 N == 2)**
    - **orderItem N번(order 조회 수 만큼)**
    - **item N번(orderItem 조회 수 만큼)**

> **참고> 지연 로딩은 영속성 컨텍스트에 있으면 영속성 컨텍스트에 있는 엔티티를 사용하고 없으면 SQL을 실행한다. 따라서 같은 영속성 컨텍스트에서 이미 로딩한 회원 엔티티를 추가로 조회하면 SQL을 실행하지 않는다.**
>