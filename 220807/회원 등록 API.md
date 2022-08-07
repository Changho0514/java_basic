# 회원 등록 API

API 개발은 

요즘엔 화면을 Template engine으로 만드는 경우도 아직 꽤 많겠지만 
`Single page application` 인 vue.js나 reactor 등으로 개발하는 경우가 훨씬 많다.
서버 개발자 입장에선 예전처럼 서버에서 렌더링을 마치고 html을 내리는 것이 아니라
그런 작업은 client, 즉, vue.js 나 reactor에게 일임한다. (프론트)

앱 개발자들 ← 당연히 API 통신해야한다.
Back 단이랑 통신해야하는 경우도 많다.

### API로 수업 구성한 이유!

우아한 형제들은 마이크로 소프트 아키텍쳐로 구성이 많이 되어 있다.

API 통신할 일도 많다.

예전처럼 SQL써서 끌고오는게 아니라 JPA를 사용하면 Entity라는 개념이 있기 때문에
이게 있는 상태에서 API 만드는 건 아예 다른 차원이에요. 주의할 것도 많고.
JPA 사용하면서 API 만들때 어떻게 하는 것이 올바른 방향인지 정리!!

우선, API를 잘 사용할 수 있는 툴을 다운로드 받자

postman 설치 ([https://www.getpostman.com](https://www.getpostman.com/))

### 상황

- **영업이 잘 되어서 APP을 만들게 되었다 → API를 만들어야겠네요**

Template engine을 사용해서 렌더링하는 Controller와
API를 사용해서 렌더링 하는 Controller Package를 구분하는 편.

→ API랑 화면이랑은 공통처리해야하는 요소가 너무 다르다
화면은 template engine에서 에러가 뜬다면 공통 에러 화면(html)이 떠야한다.
근데 API는 공통 에러 JPA 스펙이 떠야한다.

이렇게 분리해야 편한 요소들이 너무 많다 ⇒ 분리합시다!!

`@Controller + @ResponseBody` 둘이 합친 기능
⇒ `@RestController` (스프링 JPA 제공)

`@ResponseBody :` data자체를 json이나 xml로 보내자 할때 사용

`@Valid` : javax로 validation 가능.

`@RequestBody` : Json으로 온 Body를 Member에 맵핑해주어서 넣어줌. json data를 member로 바꿔준다.

```java
@Data
static class CreateMemberResponse {
    private Long id;

		public CreateMemberResponse(Long id) {
				this.id = id;
		}
}
```

→ 간단히 응답값 데이터 

```java
@PostMapping("/api/v1/members")
public CreateMemberResponse saveMemberV1(@RequestBody @Valid Member member) {
    Long id = memberService.join(member);
		return new CreateMemberResponse(id);
}
```

→ 파라미터를 받아서 등록하는 API 만들기

멤버 객체에 대한 값을 알아서 채워 넣어주니까 이렇게만 적어도 끝.

### POSTMAN 사용법

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled.png)

GET방식 옆에 [`http://localhost:8080/api/v1/members`](http://localhost:8080/api/v1/members) 를 적어주고, 

그 아래 설정 : `Body → raw → JSON` 설정

`**Headers` 는 당연히 `application/json` 으로 설정되어있어야 한다.**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%201.png)

`POST` 방식으로 `Send` 를 누르면,

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%202.png)

잘 저장되고, id가 1이 넘어온다.

### 만약 `“name”` 영역을 채워넣지 않는다면?

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%203.png)

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%204.png)

Send 하면 `“id”:2`로 들어간다

딱히 제약조건을 두지 않았기 때문!

만약 `Controller` 입장에서 제약조건을 넣고 싶다면

`[Member.java](http://Member.java)` 에서 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%205.png)

**@NotEmpty**

제약조건을 주면 된다.

다시 실행시킨 후에 다시 영역을 채워넣지 않은 상태로 다시 요청을 보내면

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%206.png)

`**400 Bad Request` 가 뜨게된다.**

### 같은 이름으로 재요청 보낸경우

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%207.png)

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%208.png)

`500 Status java.lang.IllegalStateException`: 이미 존재하는 회원입니다.

자체가 나쁘지 않은데
화면에서 들어오는 것까지 Presentation 계층이라고 하는데,
Presentation 계층에 대한 Validation 로직까지 Entity 안에 존재
→ 문제는 어떤 API에서는 **@NotEmpty**가 필요한데 어떤 API 에서는 있으면 안된다.

예를 들어 `private String name; → private String username;` 으로 바꿨을 때

**API 스펙이 달라진다** →  진짜 큰 문제!  팀별로 username2, username 등등으로 바꾼다?
→ 클라이언트 팀 입장에서 API로 호출했는데 깨진다! → API 동작이 아예 되지 않는다.

**엔티티라는건 굉장히 여러곳에서 사용하는 거에요
엔티티 - API 가 1:1로 맵핑되어있다.**

## API 스펙을 위한 별도의 DTO(Data Transfer Object)를 만들어야 한다.

- 엔티티를 이렇게 외부에서 json 오는 걸 바로 binding 해서 사용하면 안된다.
→ 나중에 큰 장애 발생!
API 요청 스펙에 맞추어 별도의 DTO를 만들어 사용합시다!
- 회원가입만 해도 “간편 회원가입" “특별 회원가입" 등등 있는데, 이걸 하나의 엔티티로 한다?
절대절대절대절대 안됩니다
- **API를 만들때는 엔티티를 절대 파라미터로 받지마세요**
- 엔티티를 바깥에 노출해서도 안된다.

## V1 엔티티를 Request Body에 직접 매핑

### 문제점

- 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.
- 엔티티에 API 검증을 위한 로직이 들어간다. (@NotEmpty 등등)
- 실무에서는 회원 엔티티를 위한 API가 다양하게 만들어지는데, 한 엔티티에 각각의 API를 위한 모든 요청 요구사항을 담기는 어렵다.
- 엔티티가 변경되면 API 스펙이 변한다.

### 결론

- API 요청 스펙에 맞추어 별도의 DTO를 파라미터로 받는다.

## DTO 사용하는 방법

```java
@PostMapping("/api/v2/members")
public CreateMemberResponse saveMemberV2(@RequestBody @Valid CreateMemberRequest request) {
    Member member = new Member();
    member.setName(request.getName());

    Long id = memberService.join(member);
    return new CreateMemberResponse(id);
}
```

- 장점
    - v1의 장점은 `CreateMemberRequest Class`를 안만드는것 뿐.
    - **Member의 스펙을 변경하면(** 예를 들어 `private String name; → private String username;` 으로 바꿨을 때) **Compile 에러가 난다.
    → DTO인 saveMemberV2 메소드에서 파라미터 조정해서 에러 없애준다.**
    - `CreateMemberRequest` 를 `Member 엔티티 대신에` RequestBody와 매핑한다.
    - 엔티티와 프레젠테이션 계층을 위한 로직을 분리할 수 있다.
    - 엔티티와 API 스펙을 명확하게 분리할 수 있다.
    - 엔티티가 변해도 API 스펙이 변하지 않는다.
    
    ## V1메소드의 단점
    
    ![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%83%E1%85%B3%E1%86%BC%E1%84%85%E1%85%A9%E1%86%A8%20API%2085a706ce2ea247f6860a286e4eb4d297/Untitled%209.png)
    
    Member는 무엇이 들어올지 모른다.
    `id, name, address, orders` 중
    무엇을 넘겨도 binding 되어 데이터가 들어간다.
    개발자 입장에서 답답한건 API 스펙문서를 까보지 않으면
    도대체 Member 중 어느 값이 넘어왔는지 몰라요.
    DTO를 사용하면
    
    ```java
    @Data
    static class CreateMemberRequest {
        private String name;
    }
    ```
    
    “아, API 스펙 자체가 그냥 name만 받게 되어있구나" 하고 알게 된다. 
    
    **> 참고: 실무에서는 엔티티를 API 스펙에 노출하면 안된다!**