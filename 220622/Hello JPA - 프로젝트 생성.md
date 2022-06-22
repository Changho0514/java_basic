# Hello JPA - 프로젝트 생성

## H2 데이터 베이스 설치, 실행

**http://www.h2database.com/**

**최고의 실습용 DB
• 가볍다.(1.5M)**

- 웹용 쿼리툴 제공
- MySQL, Oracle 데이터베이스 시뮬레이션 기능
- 시퀀스, AUTO INCREMENT 기능 지원

## **메이븐 소개**

**https://maven.apache.org/**

- 자바 라이브러리, 빌드 관리
- 라이브러리 자동 다운로드 및 의존성 관리
- 최근에는 그래들(Gradle)이 점점 유명

## 프로젝트 생성

`IntelliJ → file → new project-> maven 선택`

![Untitled](Hello%20JPA%20-%20%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC%20edd67883755c4771a7f9dd2db1a5a6fc/Untitled.png)

`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>jpa-basic</groupId>
    <artifactId>ex1-hello-jpa</artifactId>
    <version>1.0.0</version>
    <dependencies>
        <!-- JPA 하이버네이트 -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>5.3.10.Final</version>
        </dependency>
        <!-- H2 데이터베이스 -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>1.4.199</version>
        </dependency>
    </dependencies>
</project>
```

**DB를 사용하면 db에 접근할 수 있는 드라이버가 필요하므로,
H2 데이터베이스 dependency 걸어준다.
version은 다운 받은 것과 같이 맞춰준다.**

**이후 새로고침 한 후에 Maven dependencies를 확인해보면**

![Untitled](Hello%20JPA%20-%20%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC%20edd67883755c4771a7f9dd2db1a5a6fc/Untitled%201.png)

하이버네이트가 jpa 인터페이스를 갖고 있음을 확인할 수 있다.

### JPA 설정하기 -persistence.xml

- JPA 설정 파일• /META-INF/persistence.xml 위치
- persistence-unit name으로 이름 지정
- javax.persistence로 시작: JPA 표준 속성
- hibernate로 시작: 하이버네이트 전용 속성

`src→main→resources→META-INF→persistence.xml` 만들고
넣어준다.

![Untitled](Hello%20JPA%20-%20%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC%20edd67883755c4771a7f9dd2db1a5a6fc/Untitled%202.png)

`persistence.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```

필수속성 → 
db 접근조건을 말해줘야함

```xml
<property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
<property name="javax.persistence.jdbc.user" value="sa"/>
<property name="javax.persistence.jdbc.password" value=""/>
<property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
<property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

```

## 데이터베이스 방언

- **JPA는 특정 데이터베이스에 종속 X**
- 각각의 데이터베이스가 제공하는 SQL 문법과 함수는 조금씩 다름
- **가변 문자: MySQL은 VARCHAR, Oracle은 VARCHAR2**
- 문자열을 자르는 함수: SQL 표준은 SUBSTRING(), Oracle은
SUBSTR()
- **페이징: MySQL은 LIMIT , Oracle은 ROWNUM(db바꾸면 페이징을 다 바꿔야하는데 이게 거지같음)**
- **방언: SQL 표준을 지키지 않는 특정 데이터베이스만의 고유한 기능**

![Untitled](Hello%20JPA%20-%20%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC%20edd67883755c4771a7f9dd2db1a5a6fc/Untitled%203.png)