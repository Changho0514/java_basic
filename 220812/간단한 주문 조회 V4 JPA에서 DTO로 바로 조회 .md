# 간단한 주문 조회 V4: JPA에서 DTO로 바로 조회

```java
private final OrderSimpleQueryRepository orderSimpleQueryRepository; //의존관계 주입
/**V4. JPA에서 DTO로 바로 조회
쿼리 1번 호출
select 절에서 원하는 데이터만 선택해서 조회
*/
@GetMapping("/api/v4/simple-orders")
public List<OrderSimpleQueryDto> ordersV4() {
		return orderSimpleQueryRepository.findOrderDtos();
}
```

`repository package`에 Dto클래스 하나를 만드는 것이 좋다.

Dto가 Controller에 있다? → repository쪽에 해야한다.
의존관계가 repository를 controller로 보는 사태가 발생할 수 있다.

### repository에서 Controller 의존관계 생기면 안된다!

의존관계는 `Controller → Service → repository`  와 같이 한 방향으로만 흘러야 한다.

JPA는 엔티티나 Value object(embeddable) 만 기본적으로 반환할 수 있다.

dto는 반환 불가 ⇒ `new operation`을 써야한다.

### 꿀팁!

**`Column Selection mode` → `command + shift + 8` 한후에(On/off)
`shift + 방향키`를 누르면 여러 Column을 한번에 선택 가능하다.**

![Untitled](%E1%84%80%E1%85%A1%E1%86%AB%E1%84%83%E1%85%A1%E1%86%AB%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8C%E1%85%AE%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20V4%20JPA%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20DTO%E1%84%85%E1%85%A9%20%E1%84%87%E1%85%A1%E1%84%85%E1%85%A9%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%205d2e312ed2424b3e9c080e35108084d9/Untitled.png)

`[OrderSimpleQueryDto.java](http://OrderSimpleQueryDto.java)` 를 바꿔준다. 왜?
o로 하면 안된다. Order를 직접 엔티티로 받을 수 없다.
→`select new jpabook.jpashop.repository.OrderSimpleQueryDto(o) " +`

여기 o를 넣으면 식별자로 받는다. 

그래서 `OrderSimpleQueryDto` 의 생성자 파라미터를 바꿔주어야 한다.

`OrderSimpleApiController.java`

```java
@GetMapping("/api/v4/simple-orders")
public List<OrderSimpleQueryDto> ordersV4() {
    return orderRepository.findOrderDtos();
}
```

이렇게 해주면 Controller가 repository를 의존하게 된다.

V3와 V4의 차이는 **SELECT 절에서 내가 원하는것만 끌고 올 수 있다는 것!!**

fetch 조인의 경우에는 SELECT 절에서 모두 끌고오기 떄문에 아무래도 DB에서 데이터를 많이 끌어올린다!!

## ???그럼 V4가 더 좋은것 아닌가요?

- 일반적인 SQL을 사용할 때 처럼 원하는 값을 선택해서 조회
- new 명령어를 사용해서 **JPQL의 결과를 DTO로 즉시 변환**
- SELECT 절에서 원하는 데이터를 직접 선택하므로 DB 애플리케이션 네트웍 용량 최적화(생각보다 미비)
- 리포지토리 재사용성 떨어짐, **API 스펙에 맞춘 코드가 리포지토리에 들어가는 단점(쿼리를 짰지만, A**

### 정리

- 엔티티를 DTO로 변환하거나, DTO로 바로 조회하는 두가지 방법은 각각 장단점이 있다. 둘중 상황에 따라서 더 나은 방법을 선택하면 된다. 
**엔티티로 조회하면 리포지토리 재사용성도 좋고, 개발도 단순해진다.**
따라서 권장하는 방법은 다음과 같다.
- 쿼리 방식 선택 권장 순서
1. 우선 **엔티티를 DTO로 변환하는 방법을 선택**한다.
2. **필요하면 페치 조인으로 성능을 최적화 한다. `대부분의 성능 이슈가 해결된다.`**
3. **그래도 안되면 DTO로 직접 조회하는 방법을 사용한다.**
4. 최후의 방법은 JPA가 제공하는 네이티브 SQL이나 스프링 JDBC Template을 사용해서 SQL을 직접 사용한다.