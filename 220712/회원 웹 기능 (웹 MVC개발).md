# 회원 웹 기능 (웹 MVC개발)

## 1. 홈화면 추가

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled.png)

**controller package 아래에 HomeController를 추가해보자**

**@Controller** Annotation을 주고, 

```java
@GetMapping("/")
public String home() {
    return "home";
```

이를 추가 한 것은 [localhost:8080](http://localhost:8080) 으로 들어왔을 때 **도메인에서 호출되는 화면**이다.
그리고 templates에서 **home.html 이 호출** 된다

→ templates에서 `home.html`이 없으니 추가해주자

 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%201.png)

[localhost:8080](http://localhost:8080) 으로 실행시켜보면 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%202.png)

위와 같이 화면이 뜨고, 회원가입을 누르면 **/members/new** 로,
회원 목록을 누르면 **/members** 로 이동한다!

근데 이상하다. 우리 전에 **`resources/static` 경로에 index.html로**

```html
<!DOCTYPE HTML>
<html>
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>

```

이렇게 만들어놨었는데? 아무것도 없으면 welcome page로 가는거 아니었어?

<aside>
✅ 우선순위가 있다! 
**: 컨트롤러가 정적 파일보다 우선순위가 높다.**

</aside>

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%203.png)

**관련 Controller가 있는지 찾고 없으면, static file을 찾기로 했잖아요!
welcome도 마찬가지에요!**

**Home 화면에 오면 HomeController에서 먼저 mapping 된 것이 있으니 그 url로 먼저 이동한다.**
**그럼 그 url이 호출되고 끝난다. static에 있는 것들은 무시되고 끝이 난다**. 

## 2. 등록

`**templates` 패키지에 members 디렉토리를 만들고,  그 안에 createMemberForm.html을 만든다.**

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
    <form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">이름</label>
            <input type="text" id="name" name="name" placeholder="이름을
입력하세요">
        </div>
        <button type="submit">등록</button>
    </form>
</div> <!-- /container -->
</body>
</html>
```

다시 main method를 실행하면

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%204.png)

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%205.png)

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%206.png)

위와 같은 순서로 “회원가입”을 누를 수 있게된다!
”등록” 버튼을 누르면 에러가 뜨는게 당연하다.

**껍데기는 되었으니 회원등록하는 Controller가 필요해졌다!!**

**Controller package**에서 **MemberForm 을 추가하고
name 변수 / getter, setter를 만들어준다**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%207.png)

MemberController에도 아래와 같은 내용을 추가해준다.

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%208.png)

**주의! Member를 import할때는 우리가 적어주었던 hispring.domain의 Member를 추가한다**

위의 내용을 추가하고 재실행하면, 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%204.png)

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%205.png)

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%204.png)

이제 에러가 아닌 “/” 화면, 즉 home 화면이 뜨게 된다.

무슨 일이 일어났을지 살펴보자면,  MemberController에 추가한 

```java
@PostMapping("/members/new")
public String create(MemberForm form){
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/";
		}
```

이 부분이 관건일 텐데, 
memberService.join(member) 에서 **join 로직을 타고 들어가면 (커서 두고 Command+B)**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%209.png)

[memberRepository.save](http://memberRepository.save) 로직을 다시 만나게 된다. 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2010.png)

다시 한번 더 타고들어가면, **MemoryMemberRepository class의 save를 만나게 된다.
이 부분에서 충분히 잘 실행 되었을 것이다!**

### 설명!

회원가입을 들어가면 **members/new 로 들어가면 get 방식(http)으로  들어온다. 
그 뒤 mapping 되는 것은**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2011.png)

이것인데, 이 method는 하는 것 아무것도 없이

**단순히 members/createMemberForm을 return(이동) 한다!**

templates에서 createMemberForm을 찾으면, 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2012.png)

**viewResolver를 통해 이 template이 선택이 되고, thymeleaf 가 template engine이 rendering 한다.**

이 중 **form** tag는 값을 입력할 수 있는 html 태그이다.

```html
<form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">이름</label>
            <input type="text" id="name" name="name" placeholder="이름을
입력하세요">
        </div>
        <button type="s ubmit">등록</button>
    </form>
```

`**<input type="text" id="name" name="name" placeholder="이름을 입력하세요">` 이 부분은 이름 input창을 담당한다.**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2013.png)

**name=”name” 부분이 중요한데, 넘어올 때 key 역할을 한다!
placeholder는 아무것도 없을 때 적혀있는 문자를 말한다.**

`**<button type="s ubmit">등록</button>` 부분은
아래 버튼을 담당한다.**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2014.png)

**action = “/members/new” 는 이 화면에서 action이 일어날 것인데,
method=”post” 는 post 방식으로 진행될 것임을 말한다.**

해당 창에 **spring**이라고 적고 ‘등록'버튼을 누르면, post 방식으로 넘어간다.

<aside>
✅ **이게 어디로 넘어갈까??**

</aside>

MemberController의 **@PostMapping** 으로 간다!

 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2015.png)

**@GetMapping** 과 **@PostMapping**은 url이 같지만 Get/Post방식을 달리함으로서 여러 방식으로 사용할 수 있게 해준다!

**보통 조회할때 Get, 저장할때 Post 방식을 사용한다!**

**Mapping으로 호출되면 Method는 바로 실행되는것 확인!**

create(MemberForm form) 안을 살펴보면

```java
package hi.hispring.controller;

public class MemberForm {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}

```

여기서 name이 아까 적어준 “spring”으로 들어간다

**`MamberFrom.java`**

**MemberController.java**

```java
@PostMapping("/members/new")
public String create(MemberForm form){
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/";
}
```

form에서 들어온 내용을 (`private`이라 막 못꺼내니까) `getName`으로 꺼내주고, (새로운 회원을 추가 해주기 위해) member 객체를 만들어 이름을 넣어준다.

이 member를 join시키고, redirect 해서 홈화면으로 돌아간다.

## 3. 조회 기능

‘회원 목록’ 칸의 기능을 살려보자!

**MemberController.java**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2016.png)

**MemberService** 객체인 **memberService**는 `**findMembers()**` 메소드를 사용할 수 있다

**MemberService.java**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2017.png)

**getAttribute는 model에 데이터를 담을 때 사용한 것 기억하기!**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2018.png)

- **members/memberList를 리턴하는데 template에 해당 html이 없으니 만들어주자!**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2019.png)

**동작을 시켜서 “회원 목록” 을 눌렀을때 # 이름 말고는 아무것도 보이지 않는다.**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2020.png)

- 회원 가입으로 spring1, spring2를 추가하면,

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2021.png)

위와 같이 회원 목록에 추가가 되었다!

**여기서 thymleaf가 본격적으로 사용되고, template engine이 동작하는데**

```html
<tr th:each="member : ${members}">
    <td th:text="${member.id}"></td>
    <td th:text="${member.name}"></td>
</tr>
```

**th: thymleaf 문법이다  ${ } 이 문법은 모델 안에 있는 값을 꺼낼 때 사용한다.** 

`**Command + E` 를 누르면 가장 최근에 봤던 목록이 뜬다.**

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2022.png)

**MemberController를 보면,** 

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2023.png)

모든 회원을 List로 담아놨으니

`<tr th:each="member : ${members}">` List를 하나하나 루프 돌면서 객체를 member 변수에 담고,  아래 로직을 실행한다.

```java
<td th:text="${member.id}"></td>
<td th:text="${member.name}"></td>
```

출력을 해준다. 

### 해당 방법의 심각한 문제점

<aside>
✅ 서버를 내렸다가 다시 올리면?

</aside>

![Untitled](%E1%84%92%E1%85%AC%E1%84%8B%E1%85%AF%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20(%E1%84%8B%E1%85%B0%E1%86%B8%20MVC%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF)%20ec9e13a774ba428fa16c9e31461e01da/Untitled%2020.png)

**회원 목록이 깔끔하게 지워져 있다.**

**메모리 안에 있는 데이터이기 때문에**

⇒ DB를 사용할 줄 알아야한다는 거죠!