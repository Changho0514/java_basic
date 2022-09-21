# 조회 API 컨트롤러 개발

**편리한 데이터 확인을 위해 샘플 데이터를 추가하자.
샘플 데이터 추가가 테스트 케이스 실행에 영향을 주지 않도록 다음과 같이 프로파일을 설정하자**

**테스트 실행할 때, 
로컬에서 Tomcat을 띄울때랑 다른 상황으로 프로파일을 나눌거에요.**

<aside>
✅ **테스트는 기존 `application.yml`을 복사해서 다음 경로로 복사하고, 프로파일을 test로 수정하자**

</aside>

`src/main/resources/application.yml`

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled.png)

위의 부분에 추가해주자

`local`은 임의로 넣는 별칭 부분이고,

보통 `local` 은 개발단계에 있을때, 개발은 `dev` 운영은 `real` 로 쓰는 경향이 있다.

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%201.png)

### 테스트 케이스를 돌려야 할 때는

`src/test/resources/application.yml`

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%202.png)

여기에 resources 디렉토리를 만들어줘야한다.

그리고 원래 있던 `application.yml`을 복사해서 넣어준다.

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%203.png)

`active: test` 로 바꾸기!

**이렇게 분리하면 main 소스코드와 테스트 소스 코드 실행시 프로파일을 분리할 수 있다.**

데이터 초기화하기위해 
`study.querydsl.controller` 아래에
`InitMember.java` 를 생성해주자

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%204.png)

스프링 띄울때 자동으로 데이터를 다 넣어놓고 API에선 조회용 API만 호출하려고 데이터를 넣어놓는다.

이렇게 해주면

**스프링 부트로 메인을 실행하면, local이라는 프로파일로 스프링이 실행이 된다.**

그럼 `@Profile("local")` 이 동작하면서 스프링빈에 `InitMember.java`가 올라가고, 동작하면서 DB에 데이터를 다 넣고 시작할 수 있다.
그럼 조회용 API만 만들면 되지.

### 근데 왜 바로 **@PostConstruct**에 안넣었어요?

⇒ `**@Transactional**` 과 같이 넣을 수 없어요. 스프링 라이프 사이클 때문에

### Application 실행해봅시다.

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%205.png)

local로 작동하네요

## API 만들기

`study.querydsl.controller.MemberController`

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%206.png)

**그리고 메인메서드를 실행시켜보면,**

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%207.png)

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%208.png)

포스트맨으로 하면 JSON도 이쁘게 나옵니다.

1. 검색조건 **`TeamB`**

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%209.png)

1. TeamB , 나이가 31이상 35이하

![Untitled](%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20API%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%A9%E1%86%AF%E1%84%85%E1%85%A5%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20250d273bbb9547d4b596b2afc6effc87/Untitled%2010.png)