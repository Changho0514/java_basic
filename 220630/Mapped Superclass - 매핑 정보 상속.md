# Mapped Superclass - 매핑 정보 상속

상속관계 매핑과는 크게 관련은 없어요.

## **공통 매핑 정보가 필요할 때 사용(id, name)**

**클래스 만들 때마다 부모클래스에 두고 속성만 상속해서 쓰고 싶을 때.**

**db는 완전히 다른데 객체 입장에서 속성만 상속받아 쓰고 싶은거에요**

ex) **모든 곳에 등록 수정 삭제 정보를 넣고 싶을 때**

```java
private String createdBy;
private LocalDateTime createdDate;
private String lastModifiedBy;
private LocalDateTime lastModifiedDate;
```

이걸 모든 TABLE에 적용시켜 달라는 요청을 받았어요.

모두 찾아가서 복붙해야할까요? 

→ 아닙니다!

`BaseEntity.java`

```java
package jpabook.jpashop.domain;

import java.time.LocalDateTime;

@MappedSuperclass
public class BaseEntity {

    private String createdBy;
    private LocalDateTime createdDate;
    private String lastModifiedBy;
    private LocalDateTime lastModifiedDate;

    public String getCreatedBy() {
        return createdBy;
    }

    public void setCreatedBy(String createdBy) {
        this.createdBy = createdBy;
    }

    public LocalDateTime getCreatedDate() {
        return createdDate;
    }

    public void setCreatedDate(LocalDateTime createdDate) {
        this.createdDate = createdDate;
    }

    public String getLastModifiedBy() {
        return lastModifiedBy;
    }

    public void setLastModifiedBy(String lastModifiedBy) {
        this.lastModifiedBy = lastModifiedBy;
    }

    public LocalDateTime getLastModifiedDate() {
        return lastModifiedDate;
    }

    public void setLastModifiedDate(LocalDateTime lastModifiedDate) {
        this.lastModifiedDate = lastModifiedDate;
    }
}

```

를 만들구요, 

`Team, Member` 에 `extends BaseEntity` 로 상속해주시고

**@MappedSuperclass** 를 `BaseEntity.java` 에 추가 Mapped 정보만 받는 부모 클래스!!!

```java
try {

    Member member = new Member();
    member.setUsername("user1");
    member.setCreatedBy("kim");
    member.setCreatedDate(LocalDateTime.now());

    em.persist(member);

    em.flush();
    em.clear();

    tx.commit();
}
```

로 실행하면, 

![Untitled](Mapped%20Superclass%20-%20%E1%84%86%E1%85%A2%E1%84%91%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%87%E1%85%A9%20%E1%84%89%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A9%E1%86%A8%20a068a6ac1f0e4a20b7ea526e5934b5d8/Untitled.png)

![Untitled](Mapped%20Superclass%20-%20%E1%84%86%E1%85%A2%E1%84%91%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%87%E1%85%A9%20%E1%84%89%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A9%E1%86%A8%20a068a6ac1f0e4a20b7ea526e5934b5d8/Untitled%201.png)

TEAM, MEMBER TABLE에 `BaseEntity` 항목들이 잘 들어갔다!

**상속관계 매핑이 아닙니다!!! 그냥 속성만 갖다 쓰고싶어!**

## 정리

- **상속관계 매핑X**
- **엔티티X, 테이블과 매핑X**
- **부모 클래스를 상속 받는 자식 클래스에 매핑 정보만 제공**
- **조회, 검색 불가(`em.find(BaseEntity)` 불가)**
- **직접 생성해서 사용할 일이 없으므로 추상 클래스 권장**

즉, 

- 테이블과 관계 없고, 단순히 엔티티가 공통으로 사용하는 매핑 정보를 모으는 역할
- **주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용**
- 참고: @Entity 클래스는 엔티티나 @MappedSuperclass로 지정한 클래스만 상속 가능
→ 즉, JPA에서 `extends`해서 사용할 때는 **@Entity @MappedSuperclass 둘중하나가 있어야 합니다**