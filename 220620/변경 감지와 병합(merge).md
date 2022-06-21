# 변경 감지와 병합(merge)

> **참고: 정말 중요한 내용이니 꼭! 완벽하게 이해하셔야 합니다.**
> 

**엔티티가 영속상태로 관리가 된다면 
값만 바꿀 경우 JPA가 Transaction commit 시점에 변경된 내용을 DB에 반영한다.**

Book을 가져온 뒤에 트랜잭션 안에서 book.setName(

**영속성 컨텍스트에서 엔티티를 다시 조회한 후에 데이터를 수정하는 방법**

**트랜잭션 안에서 엔티티를 다시 조회, 변경할 값 선택 트랜잭션 커밋 시점에 변경 감지(Dirty Checking)**

![Untitled](%E1%84%87%E1%85%A7%E1%86%AB%E1%84%80%E1%85%A7%E1%86%BC%20%E1%84%80%E1%85%A1%E1%86%B7%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8(merge)%203497bbed4d2c4f7381d7627982a79662/Untitled.png)

`book.setName(”asdfasdf”);` 하면 트랜잭션 시점에 커밋이 되면 JPA가 이 변경점을 가지고 업데이트 쿼리를 생성해서 db에 반영을 한다 ⇒ 변경 감지
내가 원하는대로 변경이 가능. flush할때 dirty checking이란게 일어난다.

근데 문제는 준영속 엔티티에서 나온다.

`[ItemController.java](http://ItemController.java)` 에서 

![Untitled](%E1%84%87%E1%85%A7%E1%86%AB%E1%84%80%E1%85%A7%E1%86%BC%20%E1%84%80%E1%85%A1%E1%86%B7%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8(merge)%203497bbed4d2c4f7381d7627982a79662/Untitled%201.png)

Book 객체는 새로운 객체인데 `book.setId(form.getId())` 에서 식별자가 정확하게 있죠?
**db에 갔다온 객체가 식별자가 정확하게 있다면 준영속 엔티티라고한다**

## 준영속 엔티티?

**영속성 컨텍스트가 더는 관리하지 않는 엔티티를 말한다.**
(여기서는 itemService.saveItem(book) 에서 수정을 시도하는 Book 객체다. Book 객체는 이미 DB에 한번 저장되어서 식별자가 존재한다. 이렇게 임의로 만들어낸 엔티티도 기존 식별자를 가지고 있으면 준영속 엔티티로 볼 수 있다.)

**즉, 위와같이 `new Book()` 하면 JPA가 이제 관리를 안한다** 

## 준영속 엔티티를 수정하는 2가지 방법

1. 변경 감지 기능 사용
2. 병합( merge ) 사용

### 1. 변경 감지 기능 사용

```java
@Transactional
void update(Item itemParam) { //itemParam: 파리미터로 넘어온 준영속 상태의 엔티티
 Item findItem = em.find(Item.class, itemParam.getId()); //같은 엔티티를 조회한
다.
 findItem.setPrice(itemParam.getPrice()); //데이터를 수정한다.
}
```

### 2. 병합 사용

> **병합은 준영속 상태의 엔티티를 영속 상태로 변경할 때 사용하는 기능이다.**
> 

```java
@Transactional
void update(Item itemParam) { //itemParam: 파리미터로 넘어온 준영속 상태의 엔티티
 Item mergeItem = em.merge(item);
}

```

`ItemRepository.java`

![Untitled](%E1%84%87%E1%85%A7%E1%86%AB%E1%84%80%E1%85%A7%E1%86%BC%20%E1%84%80%E1%85%A1%E1%86%B7%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8(merge)%203497bbed4d2c4f7381d7627982a79662/Untitled%202.png)

item의 id를 조회했을 때

존재하지 않으면 persist

**id가 있으면 merge가 되도록 한다.**

merge를 간단하게 설명하면,

**parameter로 온 값으로 모든 데이터를 바꿔치기한다.**

![Untitled](%E1%84%87%E1%85%A7%E1%86%AB%E1%84%80%E1%85%A7%E1%86%BC%20%E1%84%80%E1%85%A1%E1%86%B7%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8(merge)%203497bbed4d2c4f7381d7627982a79662/Untitled%203.png)

## 병합 동작 방식

1. **merge() 를 실행한다.**
2. **파라미터로 넘어온 준영속 엔티티의 식별자 값으로 1차 캐시에서 엔티티를 조회한다.**

2-1.  **만약 1차 캐시에 엔티티가 없으면 데이터베이스에서 엔티티를 조회하고, 1차 캐시에 저장한다**.

1. **조회한 영속 엔티티( mergeMember )에 member 엔티티의 값을 채워 넣는다. (member 엔티티의 모든 값을 mergeMember에 밀어 넣는다. 이때 mergeMember의 “회원1”이라는 이름이 “회원명변경”으로 바뀐다.)**
2. 영속 상태인 mergeMember를 반환한다.

> 참고: 책 자바 ORM 표준 JPA 프로그래밍 3.6.5
> 

**파라미터로 넘어온 객체는 영속상태로 변하는게 아니라, 그대로 남아있고 mergeMember**

### 병합시 동작 방식을 간단히 정리

1. 준영속 엔티티의 식별자 값으로 영속 엔티티를 조회한다.
2. 영속 엔티티의 값을 준영속 엔티티의 값으로 모두 교체한다.(병합한다.)
3. 트랜잭션 커밋 시점에 변경 감지 기능이 동작해서 데이터베이스에 UPDATE SQL이 실행

## 주의

> **변경 감지 기능을 사용하면 원하는 속성만 선택해서 변경할 수 있지만, 병합을 사용하면 모든 속성이 변경된다. 병합시 값이 없으면 null 로 업데이트 할 위험도 있다. (병합은 모든 필드를 교체한다.)**
> 

- **save() 메서드는 식별자 값이 없으면( null ) 새로운 엔티티로 판단해서 영속화(persist)하고 식별자가 있으면 병합(merge)**
- **지금처럼 준영속 상태인 상품 엔티티를 수정할 때는 id 값이 있으므로 병합 수행**

> 참고: **save() 메서드는 식별자를 자동 생성해야 정상 동작한다**. 여기서 사용한 Item 엔티티의 식별자는 자동으로 생성되도록 **@GeneratedValue** 를 선언했다. **따라서 식별자 없이 save() 메서드를 호출하면 persist() 가 호출되면서 식별자 값이 자동으로 할당된다.** 반면에 식별자를 직접 할당하도록 @Id 만 선언했다고 가정하자. 이 경우 식별자를 직접 할당하지 않고, save() 메서드를 호출하면 **식별자가 없는 상태로 persist() 를 호출한다. 그러면 식별자가 없다는 예외가 발생한다.**
> 

> 참고: 실무에서는 보통 업데이트 기능이 매우 제한적이다. 그런데 병합은 모든 필드를 변경해버리고, 데이터가 없으면 null 로 업데이트 해버린다. **병합을 사용하면서 이 문제를 해결하려면, 변경 폼 화면에서 모든 데이터를 항상 유지해야 한다. 실무에서는 보통 변경가능한 데이터만 노출하기 때문에, 병합을 사용하는 것이 오히려 번거롭다.**
> 

# 가장 좋은 해결 방법

**엔티티를 변경할 때는 항상 변경 감지를 사용하세요**

- **컨트롤러에서 어설프게 엔티티를 생성하지 마세요.**
- 트랜잭션이 있는 서비스 계층에 식별자( id )와 변경할 데이터를 명확하게 전달하세요.(파라미터 or dto)트랜잭션이 있는 서비스 계층에서 영속 상태의 엔티티를 조회하고, 엔티티의 데이터를 직접 변경하세요.
- 트랜잭션 커밋 시점에 변경 감지가 실행됩니다.

```java
@PostMapping("items/{itemId}/edit")
    public String updateItem(@PathVariable Long itemId, @ModelAttribute("form") BookForm form) {

//        Book book = new Book();
//        book.setId(form.getId());
//        book.setName(form.getName());
//        book.setPrice(form.getPrice());
//        book.setStockQuantity(form.getStockQuantity());
//        book.setAuthor(form.getAuthor());
//        book.setIsbn(form.getIsbn());
//        itemService.saveItem(book);

        itemService.updateItem(itemId, form.getName(), form.getPrice(), form.getStockQuantity())
        return "redirect:/items";
    }
}
```

**주석 처리한 코드가 쉽게 정리가 된다.**

`ItemService.java`

```java
@Transactional
    public void updateItem(Long itemId, String name, int price, int stockQuantity) {
        Item findItem = itemRepository.findOne(itemId);
        findItem.setPrice(price);
        findItem.setName(name);
        findItem.setStockQuantity(stockQuantity);
    }
```

위의 코드는 좀 안좋다 

```java
findItem.change(name, price, stockQuantity);
```

위 코드처럼 하나로 해놓는 것이 나중에 추적도 쉽고, 유지보수가 쉽다.

id가 있으면 트랜잭션안에서 엔티티 조회가 되어야 영속 상태로 조회가 되고,

거기에 값을 변경해야 dirty checking이 되고, (변경감지)

트랜잭션이 commit될 때 flush 일어나면서 변경감지 일어나고 update db가 날아간다.