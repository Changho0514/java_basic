# 스코프와 Provider

첫번째 해결방안 → **Provider 사용하기**

ObjectProvider를 사용하기 

`private final MyLogger myLogger;`

→ `private final ObjectProvider<MyLogger> myLoggerProvider;` 로 바꿔주기

그럼 주입 시점에 주입 받을 수 있다.

그리고, `MyLogger myLogger = myLoggerProvider.getObject();` 를 넣어 객체를 받아온다.

**LogDemoController.java**

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}

```

이렇게 실행해도 똑같은 오류가 발생하네? 왜지?

→ **LogDemoService.java도 바꿔주자**

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final ObjectProvider<MyLogger> myLoggerProvider;

    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

Controller에 고객의 요청이 오면, HTTP가 살아있는데 살아있는 상태에서 scope를 쓸수 있는 상황이다. HTTP request 가 온 상황이니까.

`MyLogger myLogger = myLoggerProvider.getObject();` 여기서 호출하는 것이기 때문에 이제는 사용이 가능하다. 

이제 `CoreApplication` 의 `main()` 을 실행해보면, 이제 Spring이 뜬다! 

[`http://localhost:8080/log-demo`](http://localhost:8080/log-demo) 에 들어가 보면 “OK”가 떴고,

IntelliJ에는 

```java
[1f580890-26fb-4d53-a35d-2e3a239316ee] request scope bean create: hello.core.common.MyLogger@6b479dd8
[1f580890-26fb-4d53-a35d-2e3a239316ee][http://localhost:8080/log-demo]controller test
[1f580890-26fb-4d53-a35d-2e3a239316ee][http://localhost:8080/log-demo]service id = testId
[1f580890-26fb-4d53-a35d-2e3a239316ee] request scope bean close: hello.core.common.MyLogger@6b479dd8
```

와 같이 log가 남는다!

위 4줄이 한 고객의 요청이다.

[`http://localhost:8080/log-demo`](http://localhost:8080/log-demo) 을 `**command+R` 을 연타해서 고객 요청을 여러개로 만들면,**

![Untitled](%E1%84%89%E1%85%B3%E1%84%8F%E1%85%A9%E1%84%91%E1%85%B3%E1%84%8B%E1%85%AA%20Provider%200e443fe6cd93454b88e204cee305b73c/Untitled.png)

위와 같이 Id가 같은 log가 4개 단위로 남는다.

고객 요청이 오면 정확히

`MyLogger myLogger = myLoggerProvider.getObject();` 이 시점에 만들어진다.

그럼 그때 `init()` 이 호출되면서 

```java
@PostConstruct
public void init() {
    uuid = UUID.randomUUID().toString();
    System.out.println("[" + uuid + "] request scope bean create: " + this);
    }
```

uuid를 만들고, HTTP request와 연결시킨다.

그 뒤에는 setRequestURL(requestURL)이 실행되고, 

그 뒤에 log를 찍힌다.

logic도 마찬가지.

## 정리

- ObjectProvider 덕분에 `ObjectProvider.getObject()` 를 호출하는 시점까지 **request scope** 빈의 생성을 지연할 수 있다.
- `ObjectProvider.getObject()` 를 호출하는 시점에는 **HTTP 요청이 진행중이므로** request scope 빈의 생성이 정상 처리된다.
- `ObjectProvider.getObject()` 를 `LogDemoController , LogDemoService` 에서 **각각 한번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환**된다! 
⇒ 내가 직접 이걸 구분하려면 얼마나 힘들까 ㅠㅠ…
- 이 정도에서 끝내도 될 것 같지만… 개발자들의 코드 몇자를 더 줄이려는 욕심은 끝이 없다.