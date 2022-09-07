# 스프링 부트 설정 - JPA, DB

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%87%E1%85%AE%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC%20-%20JPA,%20DB%2099d442dab8b84e018d4d62d88641c56e/Untitled.png)

`src→main→resources→application.properties` 삭제해주고 `application.yml` 생성

`application.yml`

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%87%E1%85%AE%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC%20-%20JPA,%20DB%2099d442dab8b84e018d4d62d88641c56e/Untitled%201.png)

`spring.jpa.hibernate.ddl-auto: create` ⇒
옵션은 애플리케이션 실행 시점에 테이블을 drop 하고, 다시 생성한다.

> **참고: 모든 로그 출력은 가급적 로거를 통해 남겨야 한다.**
`show_sql` : 옵션은 `System.out` 에 하이버네이트 실행 SQL을 남긴다.
`org.hibernate.SQL` : 옵션은 `logger`를 통해 하이버네이트 실행 SQL을 남긴다.
> 

그래서 show_sql 옵션은 되도록 꺼준다.

### 쿼리 파라미터 로그 남기기

로그에 다음을 추가하기 `org.hibernate.type` : SQL 실행 파라미터를 로그로 남긴다.

스프링 부트를 사용하면 이 라이브러리만 추가하면 된다.

`implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.8’`

> 참고: 쿼리 파라미터를 로그로 남기는 외부 라이브러리는 시스템 자원을 사용하므로, 개발 단계에서는 편하게 사용해도 된다. 하지만 운영시스템에 적용하려면 꼭 성능테스트를 하고 사용하는 것이 좋다.
> 

추가시

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%87%E1%85%AE%E1%84%90%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC%20-%20JPA,%20DB%2099d442dab8b84e018d4d62d88641c56e/Untitled%202.png)

쿼리 아래에 이쁘게 정리되어 나온다.