# HTTP 메시지

### HTTP 메시지에 모든 것을 전송할 수 있다.

**HTML, TEXT
• IMAGE, 음성, 영상, 파일
• JSON, XML
• 거의 모든 형태의 데이터 전송 가능
• 서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용
• 지금은 HTTP 시대!**

![Untitled](HTTP%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20ebe7a779599e4d4288f5073cb1482ffe/Untitled.png)

![Untitled](HTTP%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20ebe7a779599e4d4288f5073cb1482ffe/Untitled%201.png)

- **공백은 무조건 있어야한다.**

![Untitled](HTTP%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20ebe7a779599e4d4288f5073cb1482ffe/Untitled%202.png)

![Untitled](HTTP%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20ebe7a779599e4d4288f5073cb1482ffe/Untitled%203.png)

- HTTP 메서드 (GET: 조회)
- 요청 대상 (/search?q=hello&hl=ko)
- HTTP Version(1.1)

![Untitled](HTTP%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20ebe7a779599e4d4288f5073cb1482ffe/Untitled%204.png)

- HTTP 버전
- HTTP 상태 코드: 요청 성공, 실패를 나타냄
    - 200: **성공**
    - 400: **클라이언트 요청 오류**
    - 500: **서버 내부 오류**
- 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글

## HTTP 헤더

> **header-field = field-name ":" OWS field-value OWS (OWS:띄어쓰기 허용)**
> 

![Untitled](HTTP%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5%20ebe7a779599e4d4288f5073cb1482ffe/Untitled%205.png)

- HTTP 전송에 필요한 모든 부가정보
- 예) **메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보...**

### HTTP 메시지 바디

- 실제 전송할 데이터
- HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

## 단순함. 확장 가능

- HTTP는 단순하다. 스펙도 읽어볼만하다
- HTTP 메시지도 매우 단순
- 크게 성공하는 표준 기술은 **단순하지만 확장 가능한 기술**