# Hello JPA - 애플리케이션 개발

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled.png)

JPA는

persistence클래스가 있는데,

설정 정보를 읽어서 EntityManagerFactory를 만들어 냅니다

필요할 때마다 EntityManager를 생성해서 사용하면 된다.

`src.main.java.(hello.jpa).jpaMain` 생성

```java
package hello.jpa;

public class jpaMain {
    public static void main(String[] args) {
        
    }
}
```

**설정이 잘 됐는지 확인하려면 이렇게 Main Method하나 만들어서 실행해보자**

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%201.png)

아니나 다를까 에러가 발생했다.

### 해결방법

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%202.png)

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%203.png)

**Project Structure에서 SDK를 1.8로 변경하니 해결!**

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%204.png)

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%205.png)

**db에 table을 만들어주자**

`(hello.jpa).Member`

```java
package hello.jpa;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Member {

    @Id
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

`**@Entity` → JPA가 실행될 때 관리해야 하는 친구구나 하고 인식.**

**@Id → java한테 PK가 뭔지 알려주기 위한 것!**

### 꿀팁

`command + N`을 누르면 Getter, Setter 만드는 칸이 뜬다!

`jpaMain.java`

```java
package hello.jpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

public class jpaMain {
    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        Member member = new Member();

        member.setId(1L);
        member.setName("helloA");

        em.persist(member); //저장

        em.close();

        emf.close();
    }
}

```

근데? 에러가 뜬다?

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%206.png)

**@Transaction** 이라는 단위가 JPA에서는 매우 중요하다

```java
package hello.jpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class jpaMain {
    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();
        
        
        Member member = new Member();
        member.setId(1L);
        member.setName("helloA");

        em.persist(member); //저장

        tx.commit();
        
        em.close();

        emf.close();
    }
}
```

그래서 EntityManager를 getTransaction으로 Transaction 시켜준 후에, `tx.begin();` 으로 실행시켜주고, `tx.commit();` 으로 변경사항을 적용시킨다.

실행시키면

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%207.png)

insert 쿼리문이 나온다. 이는

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%208.png)

`persistence.xml` 에서 

`show_sql`로 보여주는데, format을 `format_sql`로 보여주고,
`use_sql_comments` 는 주석으로, “이 쿼리가 나온 이유”를 말해준다.

그리고 H2에 가보면

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%209.png)

잘 등록이 되어있다.

**JPA가 매핑 정보를 보고 등록을 알아서 했네? SQL하나도 짠게 없는데**

어, 근데 나 **MEMBER** 테이블에 등록해달라고 한적은 없는데?
→ 관례를 따랐어요
원하는 테이블에 추가되기 원하면 **@Table(name= “USER”)** 식으로 Annotation을 걸어주면 됩니다!

**근데 위의 코드는 불안정한 코드입니다**

```java
package hello.jpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class jpaMain {
    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try {
            Member member = new Member();
            member.setId(1L);
            member.setName("helloA");

            em.persist(member); //저장

            tx.commit();
        } catch (Exception e) {
            tx.rollback();
        } finally {
            em.close();
        }

        emf.close();
    }
}
```

`try-catch` 를 사용하여 처리해 주시는게 좋아요.
Exception이 발생하면 `rollback`을 해주고, 

**EntityManager는 db와의 연결을 물고 동작을 하니까
다 사용하고 나면 반드시 닫아주기**

## Spring을 사용하게 되면 위의 코드는 em.persist(member); 한줄정도로 끝나요.

## 수정하고 싶다?

수정하기 전에 잘 불러올 수 있는지부터 확인하자.

```java
package hello.jpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class jpaMain {
    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try {
            Member findMember = em.find(Member.class, 1L);
            System.out.println("findMember.id = " + findMember.getId());
            System.out.println("findMember.Name = " + findMember.getName());

            tx.commit();
        } catch (Exception e) {
            tx.rollback();
        } finally {
            em.close();
        }
        emf.close();
    }
}

```

출력해보려 하면

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%2010.png)

select 쿼리가 나가서 확인이 가능하다!

> `em.remove(findMember)` 하면 delete 쿼리가 나가서 삭제된다.
> 

수정하려면

```java
try {
    Member findMember = em.find(Member.class, 1L);
    findMember.setName("HelloJPA");

    tx.commit();
}
```

하면 충분하다. `.setName();` 하고서 저장 따로 안해도 된다.
왜? → java 컬렉션 사용하고 있는 느낌이 가능하다
실행하면 

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%2011.png)

![Untitled](Hello%20JPA%20-%20%E1%84%8B%E1%85%A2%E1%84%91%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%84%8F%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20e15aeb5f6d9f445a918ed53e81f2ff63/Untitled%2012.png)

update 쿼리가 나가고, H2 db에서도 변경이 되어있다.

## 어떻게 된걸까 ?

JPA를 통해서 Entity를 가져오면 얘는 이제 JPA가 관리한다.
그리고 JPA가 변경되어있는지 안되어있는지를 Transaction이 commit하는 시점에 확인한다
변경이 되었다면 update쿼리를 JPA가 만들어서 날린다.
그 뒤에 commit 한다. 

**EntityManager는 db당 하나씩 생성된다.**

## 주의

- **엔티티 매니저 팩토리는 하나만 생성해서 애플리케이션 전체에
서 공유**
- **엔티티 매니저는 쓰레드간에 공유X (사용하고 버려야 한다).**
- **JPA의 모든 데이터 변경은 트랜잭션 안에서 실행(매우 중요!)**
- `커밋을 하셔야 적용이 됩니다.`

RDB는 트랜잭션 안에서 실행되어야 합니다.

### 가장 단순한 조회 방법

- **`EntityManager.find()`**
- 객체 그래프 탐색(a.getB().getC())

→ 그럼 **나이가 18살 이상인 회원을 모두 검색하고 싶다면?
→ JPQL 써야해용**

`List<Member> result = em.createQuery(”select m from Member”, Member.class).getResultList();`

- JPA를 사용하면 엔티티 객체를 중심으로 개발
- 문제는 검색 쿼리
- 검색을 할 때도 테이블이 아닌 **엔티티 객체를 대상으로 검색**
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능.
- 애플리케이션이 필요한 데이터만 DB에서 불러오려면 **결국 검색 조건이 포함된 SQL이 필요**하다.

### **Entity 객체를 대상으로 쿼리를 할 수 있는 JPQL이 제공된다!**

### JPQL

- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공
- SQL과 문법 유사, SELECT, FROM, WHERE, GROUP BY,
HAVING, JOIN 지원
- **JPQL은 엔티티 객체를 대상으로 쿼리**
- **SQL은 데이터베이스 테이블을 대상으로 쿼리**
- **테이블이 아닌 객체를 대상으로 검색하는 객체 지향 쿼리**
- **SQL을 추상화해서 특정 데이터베이스 SQL에 의존X**
- **JPQL을 한마디로 정의하면 객체 지향 SQL**