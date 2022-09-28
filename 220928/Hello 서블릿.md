# Hello 서블릿

스프링 부트 환경에서 서블릿 등록하고 사용해보자

> **참고**
서블릿은 톰캣 같은 웹 애플리케이션 서버를 직접 설치하고,그 위에 서블릿 코드를 클래스 파일로 빌드해서 올린 다음, 톰캣 서버를 실행하면 된다. 하지만 이 과정은 매우 번거롭다.
스프링 부트는 톰캣 서버를 내장하고 있으므로, 톰캣 서버 설치 없이 편리하게 서블릿 코드를 실행할 수 있다.
> 

### 스프링 부트 서블릿 환경 구성

**@ServletComponentScan**
스프링 부트는 서블릿을 직접 등록해서 사용할 수 있도록 **@ServletComponentScan** 을 지원한다. 다음과 같이 추가하자.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled.png)

`@ServletComponentScan` → 내 패키지를 포함해서 하위 패키지를 다 뒤져서 서블릿을 찾아 자동으로 등록할 수 있게 해준다.

### 이제 실제 서블릿을 하나 만들어보자

`hello.sevlet.basic.HelloServlet`

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%201.png)

HttpServlet을 상속받고, `helloServlet` 라는 이름을 줬다.
`urlPatterns = “hello”` 이므로 /hello로 오면 실행되는 클래스이다.

### 꿀팁

`control + O` ⇒ Override할 method를 찾을 수 있다.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%202.png)

이중 `service` 의 열쇠 모양 있는 것이 protected이다. 

직접쳐도 되지만 편리하지 않은가.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%203.png)

해석 ⇒ 이 서블릿이 호출되면 `service` 메소드가 실행된다.

`soutm` 단축키로

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%204.png)

실행되는지 확인해보자.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%205.png)

빈화면이 뜨고, 

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%206.png)

콘솔 로그창에는 helloServlet.service 가 뜬다.

> WAS가 Servlet 요청이 오면 request, response 객체를 만들어서 servlet에 던진다.
url을 호출하면 웹브라우저가 HTTP 요청메시지를 만들고 서버에 보낸다.
그럼 서버가 만들어서 보낸다.
> 

확인해보자.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%207.png)

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%208.png)

HttpServletRequest, HttpServletResponse 모두 인터페이스 이다.

톰캣 등의 WAS서버가 Servlet 표준 스펙을 구현하는 것이다.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%209.png)

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2010.png)

이렇게 보내보자.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2011.png)

굉장히 편하다. Http 메시지를 직접 파싱하면 번거로운데,
이렇게 쉽게 꺼낼 수 있다.

### 응답 메시지 보내기

**response에 값을 넣으면 웹브라우저에 응답하는 HTTP 메시지에 데이터가 담겨서 나간다.**

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2012.png)

실행해보면,

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2013.png)

개발자 도구를 들어가보자.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2014.png)

들어가서 다시 요청하면 

[https://www.notion.so](https://www.notion.so)

요청과 응답 정보를 볼 수 있다.

request Header는 웹 브라우저가 보내는 것이므로 무시하고,

Response Header를 보면, 

Content-Type이 `text/plain; charset=utf-8`이다.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2015.png)

## 정리

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2016.png)

**@WebServlet**

의 `name`, `urlPatterns`는 겹치면 안된다!

### HTTP 요청 메시지를 다 보고싶다면?

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2017.png)

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2018.png)

그리고 서버를 재시작하고 요청을 보내보자.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2019.png)

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2020.png)

## 서블릿 컨테이너 동작 방식 설명

우선 우리가 스프링 부트를 통해서 서블릿 컨테이너를 사용하였으므로,
1. 스프링부트가 내장 톰캣 서버 생성
2. 톰캣 서버는 서블릿 컨테이너를 내장하고 있다.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2021.png)

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2022.png)

우리가 보낸 요청메시지가 왼쪽과 같다.

> **그럼 서버는 `request`, `response` 객체를 만들어서 서블릿컨테이너 안에 싱글톤으로 떠있는 helloServlet을 호출해준다. 거기 `service` 메소드를 호출해주면서 request, response 객체를 넘겨준다. 필요한 작업을 하고, response 객체에다 content type, 메시지 등을 넣으면 종료되고 나가면서 WAS 서버가 데이터를 가지고 HTTP response를 만든다.**
> 

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2023.png)

### welcome 페이지 추가

지금부터 개발할 내용을 편리하게 참고할 수 있도록 welcome 페이지를 만들어두자.

`webapp` 경로에 `index.html` 을 두면 [http://localhost:8080](http://localhost:8080/) 호출시 index.html 페이지가 열린다.

![Untitled](Hello%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%BA%20f72d538802a041af9aba2e4e2f9cc6a2/Untitled%2024.png)

`index.html`

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li><a href="basic.html">서블릿 basic</a></li>
</ul>
</body>
</html>
```

`basic.html`

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>hello 서블릿
        <ul>
            <li><a href="/hello?username=servlet">hello 서블릿 호출</a></li>
        </ul>
    </li>
    <li>HttpServletRequest
        <ul>
            <li><a href="/request-header">기본 사용법, Header 조회</a></li>
            <li>HTTP 요청 메시지 바디 조회
                <ul>
                    <li><a href="/request-param?username=hello&age=20">GET - 쿼리 파라미터</a></li>
                    <li><a href="/basic/hello-form.html">POST - HTML Form</a></li>
                    <li>HTTP API - MessageBody -> Postman 테스트</li>
                </ul>
            </li>
        </ul>
    </li>
    <li>HttpServletResponse
        <ul>
            <li><a href="/response-header">기본 사용법, Header 조회</a></li>
            <li>HTTP 응답 메시지 바디 조회
                <ul>
                    <li><a href="/response-html">HTML 응답</a></li>
                    <li><a href="/response-json">HTTP API JSON 응답</a></li>
                </ul>
            </li>
        </ul>
    </li>
</ul>
</body>
</html>
```