# HTTP 응답 데이터 - API JSON

RestAPI라고도 하죠

`hello.servlet.web.response. ResponseJsonServlet`

![Untitled](HTTP%20%E1%84%8B%E1%85%B3%E1%86%BC%E1%84%83%E1%85%A1%E1%86%B8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20-%20API%20JSON%2009f360db090042aab39be507091488d3/Untitled.png)

JSON 도 사실 문자다

문자를 JSON으로 형태를 바꾸자.

`{”username”:”kim”, “age”:20}`

⇒ ObjectMapper가 필요하다 

돌려보면,

![Untitled](HTTP%20%E1%84%8B%E1%85%B3%E1%86%BC%E1%84%83%E1%85%A1%E1%86%B8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20-%20API%20JSON%2009f360db090042aab39be507091488d3/Untitled%201.png)

**HTTP 응답으로 JSON을 반환할 때는 `content-type`을 `application/json` 로 지정해야 한다.
Jackson 라이브러리가 제공하는 `objectMapper.writeValueAsString()` 를 사용하면 객체를 JSON
문자로 변경할 수 있다.**

![Untitled](HTTP%20%E1%84%8B%E1%85%B3%E1%86%BC%E1%84%83%E1%85%A1%E1%86%B8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20-%20API%20JSON%2009f360db090042aab39be507091488d3/Untitled%202.png)