# 스프링 MVC - 실용적인 방식

MVC 프레임워크 만들기에서 `v3은 ModelView를 개발자가 직접 생성해서 반환했기 때문에, 불편했던 기억이 날 것`이다. **물론 v4를 만들면서 실용적으로 개선한 기억도 날 것이다.**
스프링 MVC는 개발자가 편리하게 개발할 수 있도록 수 많은 편의 기능을 제공한다.
**실무에서는 지금부터 설명하는 방식을 주로 사용한다**

`hello.servlet.web.springmvc.v3.SpringMemberControllerV3;`

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20MVC%20-%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%20%E1%84%87%E1%85%A1%E1%86%BC%E1%84%89%E1%85%B5%E1%86%A8%20d6398d3707a64f8d9ce4fc2c4ed6a281/Untitled.png)

v2를 가져와서 하나씩 고쳐보자.

```java
@RequestMapping("/new-form")
public ModelAndView newForm() {
    return new ModelAndView("new-form");
}
```

은

```java
@RequestMapping("/new-form")
public String newForm() {
    return "new-form";
}
```

로 바꾸면 된다.

**애노테이션 기반의 컨트롤러는 ModelAndView를 반환해도 되고, 문자를 반환해도 된다. 인터페이스로 고정되어 있지 않아서 유연하다.**

**⇒ String으로 “new-form”; 만 반환해도 view이름으로 알고 프로세스가 진행된다**

save는 

```java
@RequestMapping("/save")
public ModelAndView process(HttpServletRequest request, HttpServletResponse response) {
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));

    Member member = new Member(username, age);
    memberRepository.save(member);

    ModelAndView mv = new ModelAndView("save-result");
    mv.addObject("member", member);
    return mv;
}
```

는

```java
@RequestMapping("/save")
public String save(
        @RequestParam("username") String username,
        @RequestParam("age") int age,
        Model model) {

    Member member = new Member(username, age);
    memberRepository.save(member);

    model.addAttribute("member", member);
    return "save-result";
}
```

로,

파라미터를 직접 받을 수 있다. 

모델에 바로 넘겨준다.

```java
@RequestMapping
public ModelAndView members() {

    List<Member> members = memberRepository.findAll();

    ModelAndView mv = new ModelAndView("members");
    mv.addObject("members", members);

    return mv;
}
```

는

```java
@RequestMapping
public String members(Model model) {

    List<Member> members = memberRepository.findAll();

    model.addAttribute("members", members);
    ModelAndView mv = new ModelAndView("members");
    mv.addObject("members", members);

    return "members";
}
```

로 바꿀 수 있다.

지금까지 했던 코드의 단점:

**GET, POST를 구분하지 않았다.**

지금까지는 GET으로 하건 POST로 요청하건 아무 상관 없었다.

HTTP 메서드를 구분해주는 것이 좋다

`@RequestMapping → @GetMapping, @PostMapping`

`@GetMapping, @PostMapping` 은 모두 그 안에 `@RequestMapping` 을 갖고 있다.

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20MVC%20-%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%20%E1%84%87%E1%85%A1%E1%86%BC%E1%84%89%E1%85%B5%E1%86%A8%20d6398d3707a64f8d9ce4fc2c4ed6a281/Untitled%201.png)