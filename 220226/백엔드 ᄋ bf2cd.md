# 백엔드 예제 - 회원 관리 예제

# 1. 비즈니스 요구사항 정리

- 비즈니스 로직이 있는 회원 서비스를 만들 것이고,
- 테스트 : Junit이라는 test framework으로 할 것임.
- 데이터: 회원ID, 이름
- 기능: 회원 등록, 조회
- 아직 데이터 저장소가 선정되지 않음(가상의 시나리오)
**DB가 아직 설정 안 되었다는 것** 
⇒ 성능 중요한 DB로 할지, 일반적 관계형 DB로 할지 NoSQL로 할지 아직 모르는 것!

**일반적인 웹 애플리케이션 계층 구조**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled.png)

**컨트롤러: 웹 MVC의 컨트롤러 역할
서비스 객체: 비즈니스 도메인 객체를 가지고 핵심 비즈니스 로직 구현
리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%201.png)

아직 데이터 저장소가 선정되지 않아서, 우선 **인터페이스로 구현 클래스를 변경할 수 있도록 설계
→ 인터페이스 사용이유**

**: 1.  메모리로 단순히 저장할 수 있게
  2. 금방 만들 수 있음**

데이터 저장소는 **RDB, NoSQL 등등 다양한 저장소를 고민중인 상황으로 가정**
→ 나중에 바꿔 낄 수 있음. 바꾸려면?? ⇒ interface가 필요하다!
개발을 진행하기 위해서 초기 개발 단계에서는 **구현체로 가벼운 메모리 기반의 데이터 저장소 사용**

# 2. **회원 도메인과 리포지토리 만들기**

`hi-spring→src→main→java→hi.hispring` 경로에 **`domain 패키지`**를 만들고, 
그 아래 Member를 관리할 `member 클래스`를 만든다

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%202.png)

`Long id` : System 내에 data 저장을 위한 저장된 id 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%203.png)

**Long id, name에 대해서 getter-setter method 해주고,**

`hi-spring→src→main→java→hi.hispring` 경로에 
회원 객체를 저장할 `respository 패키지`를 만들어준다
`new->interface로 MemberRepository`도 만들었다.

![MemberRepository](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%204.png)

MemberRepository

`Member save(Member member);`: 회원 저장 Method

`Optional<Member> findByID(Long id);` : id로 회원을 찾음

`Optional<Member> findByName(String name);` :name으로 회원을 찾음 

- Optional이란?
findById나 findByName이 찾은 결과가 None 이면 그대로 반환하기보다
요즘엔 Optional로 감싸서 반환하는게 선호된다.

`List<Member> findAll();` :저장된 모든 회원 리스트 반환

> **구현체를 위해서 같은 패키지 내에
MemoryMemberRepository class를 만들어준다.**
> 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%205.png)

**`implements MemberRepository후에 option+enter를 눌러주면 위와 같이 뜬다.`**
클릭해주면? **``**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%206.png)

이렇게 자동으로 완성된다.

**저장을 하기위해서 Map을 쓴다.**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%207.png)

`member.setID(++sequence)` : 저장할때 위에있는 sequence 값을 하나 올려준다. Id setting

`store.put(member.getId(), member);` 위에서 저장해준 Hashmap에 추가

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%208.png)

`Optional.ofNullable(store.get(id));`

**: id를 반환해줄때 만약 Null 값이어도 optional로 감싸주면 나중에 client가 처리할게**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%209.png)

```java
store.values().stream()
        .filter(member -> member.getName().equals(name))
        .findAny();
```

`.fliter(member → member.getName().equals(name))`
⇒ **저장되어있는 member의 name**이**(member.getName())**
**parameter로 입력한 name(equals(name))** 과 같은지 필터링

**`.findAny()`  조건 충족하면 제일 빠른애 하나.**

루프 돌면서 맵에서 하나 찾아지면 끝

### Stream이란.

- 람다를 활용할 수 있는 기술 중 하나. 자바 8 이전에는 배열 또는 컬렉션 인스턴스를 다루는 방법은 
`for` 또는 `foreach` 문을 돌면서 요소 하나씩을 꺼내서 다뤄야했음. 
간단한 경우라면 상관없지만 로직이 복잡해질수록 코드의 양이 많아져 
여러 로직이 섞이게 되고, 메소드를 나눌 경우 루프를 여러 번 도는 경우가 발생
- 스트림은 '**데이터의 흐름**’  
배열 또는 컬렉션 인스턴스에 **함수 여러 개를 조합**해서 **원하는 결과를 필터링**하고 **가공된 결과를 얻을 수** 있음. 또한 **람다를 이용해서 코드의 양을 줄이고 간결하게 표현할 수 있다**. 
즉, **배열과 컬렉션을 함수형으로 처리할 수 있다.**
- 또 하나의 장점은 간단하게 병렬처리(*multi-threading*)가 가능하다는 점
하나의 작업을 둘 이상의 작업으로 잘게 나눠서 동시에 진행하는 것을 병렬 처리(*parallel processing*)라고 한다. **즉 쓰레드를 이용해 많은 요소들을 빠르게 처리할 수 있다.**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2010.png)

`new ArrayList<>(store.values());` ⇒ **store.values는 member들임.**

# 3. **회원 리포지토리 테스트 케이스 작성**

<aside>
🔥 개발한 기능을 실행해서 테스트 할 때 **자바의 main 메서드를 통해서 실행**하거나, 웹 애플리케이션의 컨트롤러를 통해서 해당 기능을 실행한다. 
이러한 방법은 
- 준비하고 실행하는데 오래 걸리고,
- 반복 실행하기 어렵고 
- 여러 테스트를 한번에 실행하기 어렵다는 단점이 있다. 
**🏝️자바는 JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.**

</aside>

`src/test/java 경로에 repository패키지를 만든다`

관례로는 ”MemoryMemberReositoryTest” 와 같이 파일명 뒤에 Test를 붙여줘야 한다.

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2011.png)

**해석**

⇒ MemoryMemberRepository에서 MemberRepository repository를 받아온다 

그중 save 기능이 동작하는지 확인해보고 싶다!

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2012.png)

- 코드 왼쪽부분을 클릭하면 
그 줄을 실행시킬 수 있다!

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2013.png)

**`녹색불이 떴다`** ⇒ 실행이 된다 근데 아무것도 안뜨니까 코드를 좀 넣어주자

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2014.png)

`Member member = new Member();` ⇒ member 객체 생성

`member.setName(”spring”);` ⇒ member

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2015.png)

Optional에서 값을 꺼낼때는 `**.get()**` method를 이용할 수 있다.

사실 .get으로 바로 꺼내는게 별로 좋진 않지만, 여긴 test니까!

new에서 지정한 것이랑, db에서 꺼낸거랑 똑같은지 확인하기위해

System.out.println(”result =  “ + (result == member)); 를 추가한다. **(단축키:soutv)**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2016.png)

실행시켜보면 result = true가 뜬다.

근데, 내가 매번 글자로 볼 순 없으니까

### **soutv보다는 Assertions를 사용해보자**

`Assertions.*assertEquals*(member, result); 추가.` 

**기대하는 것은 new에서 저장한 member가 find method에 의해 튀어나온 값과 같아야 한다.**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2017.png)

실행시켜보면 출력되는건 없지만,

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2018.png)

녹색불이 잘 떠있다. 

만약 값이 같지 않다면, 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2019.png)

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2020.png)

이렇게 AssertionFaiedError가 뜬다!

기대했던 것 : **Expected**

실제 값 : **Actual**

## Tip1

AssertEquals method 위에 커서를 두고 `option+enter`를 하면

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2021.png)

**static import**가 가능한데, 저걸 눌러주면 맨 위에 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2022.png)

이렇게 import 되고, 그뒤에는 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2023.png)

Assertions없이 사용이 가능하다.

## Tip2

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2024.png)

 member1을 복사해서 member2를 만들때 아래 **member1에 커서를 두고 shift+F6을 누르면** 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2025.png)

해당 변수를 한번에 모두 바꿔줄 수 있다!

**Test를 계속 진행해보면,** 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2026.png)

**해석: repository에서 “name: spring1”인 애를 Member class의 result 변수로 저장한다.**

⇒ 실행이 잘 되지만, 만약 findByName(”spring2”) 였다면 assertionError가 났을 것이다.

Test class로 저장하면 좋은점이

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2027.png)

class 를 실행하면 test가 모두 실행된다.

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2028.png)

**findAll method를 생각해보자**

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2029.png)

**이름이 spring1인 member1을 생성후 저장
이름이 spring2인 member2를 생성후 저장**

repository.findAll() 한 결과를 member list “result”에 저장.

assertThat(result.size()).isEqualTo(2);  result list의 크기가 2입니까?

⇒ 현재 2개 추가해줬으니까 맞지.

근데 **전체 class를 돌려보면** 갑자기 `error`가 뜬다!!

따로는 실행이 잘됐는데...?

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2030.png)

왜지?

Test 순서 : findAll, findByName, save 순이 아니다. 순서는 보장되지 않습니다.

순서에 지장 없게 코드를 짜야합니다!!

여기선 findbyName이든 findAll이든 repository에 spring1, spring2를 이미 저장했다! 그게 문제야.

**해결방법⇒ test가 하나 끝나면 data를 clear해줘야한다!!!!!**

MemoryMemberRepository 안에 

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2031.png)

store를 정리해주는 clear코드를 추가하고, 

MemoryMemberRepositoryTest 맨 위에

![Untitled](%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8B%E1%85%A6%E1%86%AB%E1%84%83%E1%85%B3%20%E1%84%8B%20bf2cd/Untitled%2032.png)

AfterEach로 작성해준다. 하나가 끝날때마다 실행되는 callback이라고 생각하면 된다.

## 테스트코드의 중요성

**혼자 개발할때는 크게 문제가 없지만
협업 할때, 코드 줄 수가 몇 만 단위로 넘어가면 test code 없이는 개발이 불가능하다. 
Test code 공부 제대로 해놓기!!**

# 4. 회원 서비스 개발

# 5. 회원 서비스 테스트