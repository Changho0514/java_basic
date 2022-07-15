# @Configuration과 바이트 코드 조작의 마법

- 스프링 컨테이너는 싱글톤 레지스트리다.
- 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 한다.
- 그런데 스프링이 자바 코드까지 어떻게 하기는 어렵다. 
저 자바 코드를 보면 분명 3번 호출되어야 하는 것이 맞다.
- **그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용한다.**
- **모든 비밀은 @Configuration 을 적용한 AppConfig 에 있다**

**ConfigurationSingletonTest.class 에 아래 코드를 추가하자**

```java
@Test
void configurationDeep() {
 ApplicationContext ac = new
AnnotationConfigApplicationContext(AppConfig.class);
 //AppConfig도 스프링 빈으로 등록된다.
 AppConfig bean = ac.getBean(AppConfig.class);

 System.out.println("bean = " + bean.getClass());
 //출력: bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$bd479d70
}
```

출력은 원래  **`bean = class hello.core.AppConfig`** 까지만 나오는게 맞는데

뒤에 **`$$EnhancerBySpringCGLIB$$bd479d70`** 얘는 뭘까?

- 사실 AnnotationConfigApplicationContext 에 파라미터로 넘긴 값은 스프링 빈으로 등록된다. 그래서 AppConfig 도 스프링 빈이 된다.
- 이것은 내가 만든 클래스가 아니라 **스프링이 CGLIB라는** 
**바이트코드 조작 라이브러리를 사용해서** AppConfig 클래스를 상속받은 
**임의의 다른 클래스를 만들고**, **그 다른 클래스를 스프링 빈으로 등록한 것**이다!

![Untitled](@Configuration%E1%84%80%E1%85%AA%20%E1%84%87%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%90%E1%85%B3%20%E1%84%8F%E1%85%A9%E1%84%83%E1%85%B3%20%E1%84%8C%E1%85%A9%E1%84%8C%E1%85%A1%E1%86%A8%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A1%E1%84%87%E1%85%A5%E1%86%B8%20a1518c82c14f4e86939729910db53a84/Untitled.png)

**그 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해준다.** 아마도 다음과 같이 바이트 코드를 조작해서 작성되어 있을 것이다.(실제로는 CGLIB의 내부 기술을 사용하는데 매우 복잡하다.)

**AppConfig@CGLIB 예상 코드**

```java
@Bean
public MemberRepository memberRepository() {

 if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
 return 스프링 컨테이너에서 찾아서 반환;
 } else { //스프링 컨테이너에 없으면
 기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
 return 반환
 }
}
```

**@Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고**, 
**스프링 빈이 없으면 생성해서 스프링 빈으로 등록**하고 **반환하는 코드가 동적으로 만들어진다**

**덕분에 싱글톤이 보장되는 것이다.**

## 그럼 질문 하나!

> **@Configuration 을 적용하지 않고, @Bean 만 적용하면 어떻게 될까?**
@Configuration 을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해서 싱글톤을 보장하지만, 만약 @Bean만 적용하면 어떻게 될까?
> 

**AppConfig의 @Configuration 삭제 후에 실행하면**

> bean = class hello.core.AppConfig
> 

**순수한 AppConfig로 스프링 빈에 등록되고,**

> call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
call AppConfig.memberRepository
> 

**→ Singleton이 꺠졌다..!**

**인스턴스가 같은지 테스트 결과**

```java
memberService -> memberRepository =
hello.core.member.MemoryMemberRepository@6239aba6
orderService -> memberRepository =
hello.core.member.MemoryMemberRepository@3e6104fc
memberRepository = hello.core.member.MemoryMemberRepository@12359a82
```

<aside>
✅ **당연히 인스턴스가 같은지 테스트 하는 코드도 실패하고, 각각 다 다른 MemoryMemberRepository 인스턴스를 가지고 있다.**

</aside>

### 정리

- **@Bean만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지 않는다**.
- **memberRepository() 처럼 의존관계 주입이 필요(@Autowired)해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않는다.**
- 크게 고민할 것이 없다. **스프링 설정 정보는 항상 @Configuration 을 사용하자**.