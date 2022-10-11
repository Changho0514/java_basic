# HTTP 요청 메시지 - 단순 텍스트

서블릿에서 학습한 내용을 떠올려보자.

- HTTP message body에 데이터를 직접 담아서 요청
    - HTTP API에서 주로 사용, JSON, XML, TEXT
    - 데이터 형식은 주로 JSON 사용
    - POST, PUT, PATCH
    

요청 파라미터와 다르게, HTTP 메시지 바디를 통해 데이터가 직접 넘어오는 경우는 **@RequestParam** ,
**@ModelAttribute** 를 사용할 수 없다. (물론 HTML Form 형식으로 전달되는 경우는 요청 파라미터로
인정된다.)

먼저 가장 단순한 텍스트 메시지를 HTTP 메시지 바디에 담아서 전송하고, 읽어보자.
HTTP 메시지 바디의 데이터를 `InputStream` 을 사용해서 직접 읽을 수 있다.

근데 Stream은 바이트 코드이다 ⇒ 쓸때는 항상 어떻게 인코딩해서 가져올지를 말해줘야한다.

`StreamUtils.*copyToString*(inputStream, StandardCharsets.*UTF_8*);`

`hello.springmvc.basic.request.RequestBodyStringController`

![Untitled](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20-%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%89%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20df360fa787eb4509a0576431ef92fa96/Untitled.png)

저번 Servlet으로 했던것과 같다. 

Postman 에서 `Body → raw, Text`로 바꿔주자.

![Untitled](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20-%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%89%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20df360fa787eb4509a0576431ef92fa96/Untitled%201.png)

![Untitled](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20-%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%89%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20df360fa787eb4509a0576431ef92fa96/Untitled%202.png)

응답이 잘 찍힌다.

### Input, Output 스트림, Reader - requestBodyStringV2

서블릿이 통으로 필요한게 아니니까 `InputStream`을 직접 받는다.

![Untitled](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20-%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%89%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20df360fa787eb4509a0576431ef92fa96/Untitled%203.png)

깔끔해진다.

### HttpEntity - requestBodyStringV3

![Untitled](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20-%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%89%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20df360fa787eb4509a0576431ef92fa96/Untitled%204.png)

스프링에 제공하는 기능

`HttpEntity<String> httpEntity` 이걸 파라미터로 넣어주면 

“HTTP 바디에 있는 걸 String으로 바꿔서 넣어줄게” [HTTP 메세지 컨버터] 

스프링 MVC는 다음 파라미터를 지원한다.

- `HttpEntity`: HTTP header, body 정보를 편리하게 조회
**메시지 바디 정보를 직접 조회**
요청 파라미터를 조회하는 기능(@RequestParam, @ModelAttribute)과 관계 없음
- HttpEntity는 응답에도 사용 가능
**메시지 바디 정보 직접 반환**
**헤더 정보 포함 가능**
**view 조회X**

HttpEntity 를 상속받은 다음 객체들도 같은 기능을 제공한다.
`RequestEntity`
- HttpMethod, url 정보가 추가, 요청에서 사용
`ResponseEntity`
- HTTP 상태 코드 설정 가능, 응답에서 사용
`return new ResponseEntity<String>("Hello World", responseHeaders,
HttpStatus.CREATED)`

> 참고
스프링MVC 내부에서 HTTP 메시지 바디를 읽어서 문자나 객체로 변환해서 전달해주는데, 이때 HTTP
메시지 컨버터( `HttpMessageConverter` )라는 기능을 사용한다. 이것은 조금 뒤에 HTTP 메시지
컨버터에서 자세히 설명한다.
> 

### @RequestBody - requestBodyStringV4

![Untitled](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20-%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%89%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%A6%E1%86%A8%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20df360fa787eb4509a0576431ef92fa96/Untitled%205.png)

### **@RequestBody**

**@RequestBody** 를 사용하면 HTTP 메시지 바디 정보를 편리하게 조회할 수 있다. 참고로 헤더 정보가
필요하다면 HttpEntity 를 사용하거나 **@RequestHeader** 를 사용하면 된다.
이렇게 메시지 바디를 직접 조회하는 기능은 요청 파라미터를 조회하는 **@RequestParam ,
@ModelAttribute** 와는 전혀 관계가 없다.

### 요청 파라미터 vs HTTP 메시지 바디

요청 파라미터를 조회하는 기능: **@RequestParam , @ModelAttribute**
HTTP 메시지 바디를 직접 조회하는 기능: **@RequestBody, @ResponseBody**
**@ResponseBody** 를 사용하면 응답 결과를 HTTP 메시지 바디에 직접 담아서 전달할 수 있다.
물론 이 경우에도 `view`를 사용하지 않는다