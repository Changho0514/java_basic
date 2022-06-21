# JPA 소개

JAVA Persistence API

### 자바 진영의 ORM 기술 표준

### ORM?

- **Object-relational mapping(객체 관계 매핑)**
- **객체는 객체대로 설계**
- **관계형 데이터베이스는 관계형 데이터베이스대로 설계**
- **ORM 프레임워크가 중간에서 매핑**
- **대중적인 언어에는 대부분 ORM 기술이 존재**

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled.png)

**개발자가 직접 JDBC API를 사용하는 것이 아니라 JPA가 JDBC API를 받아서 SQL을 호출하고 DB에서 결과를 받아서 동작한다.**

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%201.png)

### JPA를 쓰게 되면

ex) JPA에게 멤버 객체를 넘김 → JPA 가 멤버 객체를 분석 → JPA가 적절한 INSERT 쿼리 생성 → JDBC API 사용 → DB에 보낸다.

사실 JPA도 용 빼는 재주가 있는 것이 아니라
JPA 도 사실 JDBC API 를 이용하는 것.

## **중요한 건, 쿼리를 개발자가 만드는게 아니라 JPA가 만들어줘요 그리고, 패러다임의 불일치를 해결해준다.**

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%202.png)

- EJB는 아마추어 같은 기술이었음.
- ORM 프레임 워크를 만듬 → 하이버네이트(오픈소스)
- 자바 진영에서 반성해서 만듦. 거의 복붙이긴 해요.

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%203.png)

**JPA는 인터페이스의 모음?
→ 실제로 동작하는 게 아니라 껍데기, 스펙의 모음이다**

80-90% 이상을 하이버네이트를 씁니다!

## JPA를 왜 사용해야 하는가?

- **SQL 중심적인 개발에서 객체 중심으로 개발**
- **생산성**
- **유지보수**
- **패러다임의 불일치 해결**
- **성능**
- **데이터 접근 추상화와 벤더 독립성**
- **표준**

### 생산성 - JPA와 CRUD

- **저장: `jpa.persist(member)`**
- **조회: `Member member = jpa.find(memberId)`**
- **수정: `member.setName(“변경할 이름”)`**
- **삭제: `jpa.remove(member)`**

이미 다 만들어져 있음.
”수정” 하는 것도 매우 편하다.

`member.setName(“변경할 이름”)` ← 알아서 update 쿼리가 된다.

### 유지보수 - 기존: 필드 변경시 모든 SQL 수정

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%204.png)

악덕 기획자가 있다면 프로젝트 끝날 쯤에 기능 하나를 추가해 달라고 함.
→ SQL 하나하나 다 고쳐야 했음.

### 유지보수 - JPA: 필드만 추가하면 됨, SQL은 JPA가 처리

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%205.png)

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%206.png)

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%207.png)

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%208.png)

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%209.png)

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%2010.png)

**JPA를 통해서 member 객체를 가져온 것이라면 객체 그래프를 자유롭게 탐색할 수 있다.  “지연로딩”이라는 기능(객체 조회, 사용하는 시점에 SQL이 나가서 데이터를 채워주는)덕분에 그렇다.**

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%2011.png)

**JPA가 같은 트랜잭션에서 조회한 엔티티는 같음을 보장해준다.**

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%2012.png)

## **계층 사이에 중간 계층이 존재하면,** 모아서 쓰는 버퍼링, 캐싱기능이 있다.

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%2013.png)

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%2014.png)

**버퍼링 기능 → memberA 등록 하면 왔다갔다 해야하는데 그걸 한번에 다 등록하면 한번만 갔다오면 되니까**

![Untitled](JPA%20%E1%84%89%E1%85%A9%E1%84%80%E1%85%A2%2045a415de2c2c4b4c8fbc4cf1346b7e1c/Untitled%2015.png)

지연로딩은 네트워크를 2번 탄다.
그래서, **MEMBER를 사용할 때 TEAM을 자주 사용하면 즉시 로딩이 유리하고
자주 사용하지 않으면 지연 로딩이 유리하다.**

JPA 공부 열심히 하더라도 RDB 공부도 꾸준히, 계속 해야합니다.

ORM은 객체와 RDB에 대한 이해가 바탕이 되어야 합니다.