# MVC 패턴 - 적용

`서블릿`을 컨트롤러로 사용하고, `JSP`를 뷰로 사용해서 MVC 패턴을 적용해보자.
`Model`은 `HttpServletRequest` 객체를 사용한다. **request는 내부에 데이터 저장소를 가지고 있는데**, `request.setAttribute()` , `request.getAttribute()` 를 사용하면 데이터를 보관하고, 조회할 수 있다.

### 회원 등록

`hello.servlet.web.servletmvc.MvcMemberFormServlet`

```java
@WebServlet(name = "MvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
public class MvcMemberFormServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

`**dispatcher.forward()**` : 다른 서블릿이나 JSP로 이동할 수 있는 기능이다. **서버 내부에서 다시 호출이 발생한다.** 서블릿에서 JSP를 호출했음.

> `**/WEB-INF**`
이 경로안에 JSP가 있으면 **외부에서 직접 JSP를 호출할 수 없다**. 우리가 기대하는 것은 항상 컨트롤러를 통해서 JSP를 호출하는 것이다.

`**redirect vs forward**`
**리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다. 따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다.** **반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다. 마치 메서드, 함수가 호출 되듯이 한번 실행된다.**
> 

### `/WEB-INF`

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled.png)

“`new-form.jsp` 가 그냥 불려지고 싶지 않다. 컨트롤러를 통해서만 호출하고싶다.” 한다면, 이 경로에 넣어놔야 한다. (대문자 필수)

이건 WAS 서버 공통 룰이다.
WEB-INF 아래에 있는 자원들은 외부에서 호출해도 호출 되지 않는다.

<aside>
✅ 호출해도 호출되지 않는다!

</aside>

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%201.png)

항상 servlet을 거쳐서 내부에서 호출해야만 실행이 된다. 

고객 요청이 오면 service가 호출되는데, 

`RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);` 이 dispatcher가 내부에서 

`String viewPath = "/WEB-INF/views/new-form.jsp";` 를 다시 호출한다. 

서버 내부에서 서버끼리 호출한다. 제어권을 내어준다(?)

### 회원 등록 폼 - 뷰

`main/webapp/WEB-INF/views/new-form.jsp`

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<!-- 상대경로 사용, [현재 URL이 속한 계층 경로 + /save] -->
<form action="save" method="post">
    username: <input type="text" name="username" />
    age: <input type="text" name="age" />
    <button type="submit">전송</button>
</form>
</body>
</html>
```

`.jsp` 파일은 아래문구가 꼭 있어야하는 것 기억하세요.

`<%@ page contentType="text/html;charset=UTF-8" language="java" %>`

이대로 돌려보자.

### 상대경로 사용

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%202.png)

/save 면 절대경로로 들어간다. [`http://localhost:8080/](http://localhost:8080/)save` 이렇게

/가 없이 save만 쓰면

[http://localhost:8080/servlet-mvc/members/new-form](http://localhost:8080/servlet-mvc/members/new-form) 여기서 썼을 때는

[http://localhost:8080/servlet-mvc/members/](http://localhost:8080/servlet-mvc/members/new-form)save 로 들어가게 된다.

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%203.png)

### 주의!

> 이후 코드에서 해당 jsp를 계속 사용하기 때문에 상대경로를 사용한 부분을 그대로 유지해야 한다
> 

## 회원 저장 컨트롤러 만들기

`hello.servlet.web.servletmvc.MvcMemberSaveServlet`

```java
@WebServlet(name = "MvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
public class MvcMemberSaveServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        //Model에 데이터를 보관한다.
        request.setAttribute("member", member);

        String viewPath = "WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

```java
String username = request.getParameter("username");
int age = Integer.parseInt(request.getParameter("age"));

Member member = new Member(username, age);
memberRepository.save(member);
```

여기까지는 `MemberSaveServlet` 과 같다.

다른점은 Model에 데이터 보관하는 부분부터.

`request.setAttribute("member", member);` 하면 request 객체 내부에 있는 저장소. Map 같은게 있는데 거기에 저장해준다.

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%204.png)

## 회원저장 뷰 만들기

`main/webapp/WEB-INF/views/save-result.jsp`

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body>
성공
<ul>
    <li>id=${member.id}</li>
    <li>username=${member.username}</li>
    <li>age=${member.age}</li>
</ul>
<a href="/index.html">메인</a>
</body>
</html>
```

`<%= request.getAttribute("member")%>` 로 모델에 저장한 member 객체를 꺼낼 수 있지만, 너무 복잡해진다.
JSP는 `${}` 문법을 제공하는데, 이 문법을 사용하면 request의 attribute에 담긴 데이터를 편리하게 조회할 수 있다.

`MvcMemberSaveServlet` 을 보면

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%205.png)

**파라미터를 받고, 비즈니스 로직을 호출하고, Model에 데이터를 보관해서
뷰에 던지는 것까지 컨트롤러의 역할을 충실히 하고있다.**

`save-result.jsp`를 보면

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%206.png)

화면을 보여주기 위한 것들만 깔끔히 갖고 있다.

**MVC 덕분에 컨트롤러 로직과 뷰 로직을 확실하게 분리한 것을 확인할 수 있다. 향후 화면에 수정이 발생하면 뷰 로직만 변경하면 된다.**

## 회원 목록 조회

### 회원 목록 조회 컨트롤러 만들기

```java
@WebServlet(name = "MvcMemberListServlet", urlPatterns = "servlet-mvc/members")
public class MvcMemberListServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        List<Member> members = memberRepository.findAll();

        //모델에 담기
        request.setAttribute("members", members);

        //viewPath 잡기
        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

`request.setAttribute("members", members);` 
앞의 “members” 가 키가 되고, 뒤의 members는 값이 된다.

### 회원 조회 뷰 만들기.

`main/webapp/WEB-INF/views/members.jsp`

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<a href="/index.html">메인</a>
<table>
    <thead>
    <th>id</th>
    <th>username</th>
    <th>age</th>
    </thead>
    <tbody>
    <c:forEach var="item" items="${members}">
        <tr>
            <td>${item.id}</td>
            <td>${item.username}</td>
            <td>${item.age}</td>
        </tr>
    </c:forEach>
    </tbody>
</table>
</body>
</html>
```

모델에 담아둔 members를 JSP가 제공하는 `taglib`기능을 사용해서 반복하면서 출력했다.
members 리스트에서 member 를 순서대로 꺼내서 `item` 변수에 담고, 출력하는 과정을 반복한다.
`<c:forEach>` 이 기능을 사용하려면 다음과 같이 선언해야 한다.
`<%@ taglib prefix="c" uri="[http://java.sun.com/jsp/jstl/core](http://java.sun.com/jsp/jstl/core)"%>`

![Untitled](MVC%20%E1%84%91%E1%85%A2%E1%84%90%E1%85%A5%E1%86%AB%20-%20%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%AD%E1%86%BC%202abc3f98c343425690661aa727d3701a/Untitled%207.png)

참고

> **앞서 설명했듯이 JSP를 학습하는 것이 이 강의의 주 목적이 아니다. JSP가 더 궁금한 분들은 이미 수 많은 자료들이 있으므로 JSP로 검색하거나 관련된 책을 참고하길 바란다. (반나절이면 대부분의 기능을 학습할 수 있다.)**
>