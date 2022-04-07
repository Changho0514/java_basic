# AOP

<aside>
✅ **AOP가 필요한 상황**

</aside>

**모든 메소드의 호출 시간을 측정하고 싶다면?**
공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)
회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?

메소드가 1000개 된다고 하면 시작과 끝에 “시간 측정 로직”을 다 집어넣어야해? ⇒ 초단위로 다 찍어놨는데?
⇒ (ms) 단위로 바꿔주세요 하면 1000개를 또 반복해야하나요?

**시간 재는 로직 만들기**

```java
public class MemberService {
 /**
 * 회원가입
 */
		public Long join(Member member) {
				long start = System.currentTimeMillis();
				try {
						 validateDuplicateMember(member); //중복 회원 검증
						 memberRepository.save(member);
						 return member.getId();
				} finally {
						 long finish = System.currentTimeMillis();
						 long timeMs = finish - start;
						 System.out.println("join " + timeMs + "ms");
			  }
		}
	 /**
	 * 전체 회원 조회
	 */
	 public List<Member> findMembers() {
			 long start = System.currentTimeMillis();
			 try {
					 return memberRepository.findAll();
			 } finally {
					 long finish = System.currentTimeMillis();
					 long timeMs = finish - start;
					 System.out.println("findMembers " + timeMs + "ms");
			 }
	 }
}
```

**⇒ 문제**

1. **회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 기능이 아니다.**
2. **시간을 측정하는 로직은 공통 관심 사항이다. (여러 메소드에 걸침)**
3. **시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.**
4. **시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.**
5. **시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다**

이걸 1000개를 손으로 써서 완성했는데....

<aside>
✅ 근데 누가 나타나서 “어 그거 AOP쓰면 되는데요...?”

</aside>

## AOP 적용

**AOP: Aspect Oriented Programming**
**공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)** 분리

![Untitled](AOP%208dbcd/Untitled.png)

**시간 측정 AOP 등록**

> Package를 aop라고 하나 만들어주자
> 

```java
package hello.hellospring.aop;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class TimeTraceAop {
	 @Around("execution(* hello.hellospring..*(..))")
	 public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
			 long start = System.currentTimeMillis();
			 System.out.println("START: " + joinPoint.toString());
			 try {
					 return joinPoint.proceed();
			 } finally {
					 long finish = System.currentTimeMillis();
					 long timeMs = finish - start;
					 System.out.println("END: " + joinPoint.toString()+ " " + timeMs +
	"ms");
	 }
 }
}
```

**@Around** 는 Target이 될 친구를 넣어준다. **“이 패키지 하위에는 다 적용해”**

**⇒ 해결**

1. **회원가입, 회원 조회등 핵심 관심사항**과 **시간을 측정하는 공통 관심 사항**을 **분리**한다.
2. 시간을 측정하는 로직을 **별도의 공통 로직으로** 만들었다.
3. **핵심 관심 사항을 깔끔하게 유지할 수 있다.**
4. 변경이 필요하면 이 로직만 변경하면 된다.
5. 원하는 적용 대상을 선택할 수 있다

어떻게 동작하는 걸까?

**AOP 적용 전 의존관계**

![Untitled](AOP%208dbcd/Untitled%201.png)

![Untitled](AOP%208dbcd/Untitled%202.png)

**프록시라는 가짜 Spring Bean을 만들어 내는것!
그 뒤 joinPoint.proceed()하면 진짜 호출해줌**

![Untitled](AOP%208dbcd/Untitled%203.png)