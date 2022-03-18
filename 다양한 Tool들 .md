# 다양한 Tool 들(클래스,

# 1. String Class

> 문자열의 경우에는 **‘문자열 리터럴(String Literal)'**로 만들기 때문에 기본형이 아닌가 오해를 할 수 있다.
하지만 문자열도 `**클래스 기반의 자료형**`이기 때문에 여러 가지 메소드를 사용할 수 있다.
> 

## 1) 대소문자 변환

**`toUpperCase`** 메소드를 이용하면 모든 글자를 대문자로, 
**`toLowerCase`** 메소드를 이용하면 모두 소문자로 변환할 수 있다.

```java
String myString = "aBc";
System.out.println(myString.toUpperCase())// 모두 대문자로
System.out.println(myString.toLowerCase())// 모두 소문자로
System.out.println(myString)// 그대로
```

```
ABC
abc
aBc

```

다만 이 두 메소드는 **`myString`** 자체를 바꾸는 것이 아니라, 새로운 문자열을 **리턴시켜주는 것이다.** 
따라서 마지막에 **`System.out.println(myString)`**을 하면 원래대로 **`"aBc"`**가 나온다.

## 2) **문자열 비교**

```java
String myString = "aBc";
System.out.println(myString.toLowerCase() == "abc");
```

```
false

```

양쪽 다 **`"abc"`**이니까 **`true`**가 나올 것 같은데, 실행하면 **`false`**가 나온다.
기본형의 비교 연산자는 **양쪽의 값**이 같으면 **`true`**가 나오는데, 
**참조형의 비교 연산자는 가리키는 인스턴스가 같은 인스턴스인지 확인하는 역할을 합니다.** 
위의 예시에서 두 인스턴스가 서로 다른 인스턴스이기 때문에 **`false`**가 나온다.
만약 두 문자열의 **내용**이 같은지 비교하려면 **`==`**가 아니라 **`equals`** 메소드를 사용해야 한다.

```java
String myString = "aBc";
System.out.println(myString.toLowerCase().equals("abc"));
```

```
true
```

예전에 'DNA 염기 서열' 문제에서는 **`equals`**를 쓰지 않고 **`switch`**문에 넣었는데, 이건 왜 제대로 작동했을까? 

<aside>
✅ **`switch`**문 내부에서 **`==`** 대신 **`equals`** 메소드를 쓰고 있었기 때문이다!

</aside>

# 2. 숫자 다루는 도구

## 1) **절댓값**

```java
import java.lang.Math;

public class Driver {
    public static void main(String[] args) {
        System.out.println(Math.abs(-10));
        System.out.println(Math.abs(8));
    }
}

```

```
10
8

```

## 2) **최솟값, 최댓값**

 **`Math`** 클래스에는  **`max`** 메소드와 **`min`** 메소드 또한 존재한다.

```java
import java.lang.Math;

public class Driver {
    public static void main(String[] args) {
        System.out.println(Math.min(4, 10));// 최솟값
        System.out.println(Math.max(4, 10));// 최댓값
    }
}

```

```
4
10

```

## 3) **Random 클래스**

임의의 값을 받아오려면 **`Random`** 클래스를 **`import`** 해야한다. 
그런데 **`Math`**와는 달리 **`Random`**은 인스턴스를 생성해서 사용해야 한다.
**`0`** 이상 **`n`** 이하의 랜덤 값을 받아오려면 **`Random`** 클래스에 있는 **`nextInt`** 메소드가 적합하다.

```java
import java.util.Random;

public class Driver {
    public static void main(String[] args) {
        Random rand = new Random();
        System.out.println(rand.nextInt(10));// 0 이상 9 이하의 랜덤한 값
    }
}

```

## 4) **`a`와 `b` 사이의 랜덤 값**

그럼 **`0`** 이상 **`n`** 이하가 아니라, **`a`** 이상 **`b`** 이하의 랜덤 값을 받아오려면 어떻게 해야할까?

```java
import java.util.Random;

public class Driver {
    public static void main(String[] args) {
        Random rand = new Random();
        int min = 10;
        int max = 30;

        System.out.println(rand.nextInt((max - min) + 1) + min);// 10 이상 30 이하의 랜덤한 값
    }
}

```

<aside>
✅ **해석**

</aside>

$`System.out.println(rand.nextInt((max - min) + 1) + min);`$의 의미는

`**rand.nextInt(21)` 을 해줘서 0-20사이의 랜덤한 값을 가져오고,**

**거기에 10을 더해서 출력하는 것이다.**

# 3. Wrapper Class

'Wrapper 클래스'는 **기본 자료형을 객체 형식으로 감싸는 역할**을 한다.

**`Integer`** 클래스는 **`int`**형을, **`Double`** 클래스는 **`double`**을, 
**`Long`** 클래스는 **`long`**을, **`Boolean`** 클래스는 **`boolean`**을 감싼다

<aside>
✅ **이게 왜 필요해요?**

</aside>

**기본형 자료형(Primitive Type)을 참조형(Reference Type)처럼 다루어야할 때 
Wrapper 클래스를 사용한다!** 
예를 들어**`*ArrayList`**같은 컬렉션을 사용할 때 꼭 참조형을 사용해야* 한다.

**Wrapper 클래스의 인스턴스는 생성자로 생성할 수도 있고, 리터럴로 생성할 수도 있다.**

```java
Integer i = new Integer(123);
Integer i = 123;
```

**주의할 점은**

```java
System.out.println(123 == 123);
System.out.println(new Integer(123) == new Integer(123));

```

```
true
false

```

첫 번째 줄의 결과값은 예상대로 **`true`**
그런데 왜 두 번째 줄은 **`false`**가 나올까? 
참조형의 비교 연산자는 **가리키는 객체가 같읕지 비교하기 때문
두 생성자를 통해 만든 다른 객체이기 때문에** **`false`**가 나온다.

두 **`String`** 인스턴스를 비교할 때처럼 **`equals`** 메소드를 쓰면 '값'이 같은지 확인할 수 있다.

```java
System.out.println(new Integer(123).equals(new Integer(123)));

```

```
true
```

# 4. ArrayList

**배열**은 변수 하나에 값 여러개를 저장할 수 있어 유용하지만

### 단점

- 크기를 처음에 정해야한다.
- 크기는 변경이 불가능하다
- 채워넣지 않은 값들은 Null이 되어 찝찝하다

이 **배열의 단점을 극복한 자료형**이 **ArrayList**이다.

## 1) 문자열 넣기

```java
import java.util.ArrayList;

public class Main {

		public static void main(String[] args) {
				ArrayLIst<String> nameList = new ArrayList<>();
				nameList.add("최창호");
				nameList.add("정현교");
        nameList.add("신동운");
        nameList.add("이준경");
        nameList.add("신원일");

				System.out.println(nameList.size());
				System.out.println(nameList.get(0));
		}
}
```

```
5
최창호
```

문자열을 담을 것이기 때문에

**`ArrayList<String>` 으로 사용하였고, 이 외에도 remove, clear등등 다양한 메소드는
”nameList.”를 찍어보면 사용가능한 메소드들이 나온다.**

## 2) 숫자형 넣기

```java
import java.util.ArrayList;

public class Main {

    public static void main(String[] args) {
        ArrayList<Integer> numList = new ArrayList<>();
        numList.add(1);
        numList.add(2);
        numList.add(3);
        numList.add(4);
        numList.add(5);

        for (int num : numList) {
						System.out.println(num * num);
				}
    }
}

```

```java
1
4
9
16
25
```

**`ArrayList<Integer>` 어? 이거 Wrapper class 잖아?
기본형이 ArrayList에 들어가야 하기 때문에 Wrapper class를 거쳐야 한다!!**

## 예제 문제

# 살아남아보자

*전쟁에서 처참한 패배를 겪고 돌아온 장군. 책임을 지기 위해 살아 돌아온 이들에게 자결을 제안하는데...*

**`n`**명의 군사들이 동그랗게 서있고, 한 명씩 세어 나가서 매 **`k`** 번째 사람이 죽기로 합니다. 예를 들어서 8명의 군사들이 있고 3명마다 죽기로 하면 이 순서로 군사들이 죽게 됩니다.

```
3 => 6 => 1 => 5 => 2 => 8 => 4 => 7

```

하지만 야비한 장군은 자신이 마지막으로 남아서 도망가려는 속셈인데요. 
파라미터로 정수 **`n`**과 정수 **`k`**를 받고, 장군이 살기 위해 서있어야할 자리(**`int`**)를 리턴해주는 메소드 **`getSurvivingIndex`**를 쓰세요.

**`ArrayList`를 사용하세요!**

```java
import java.util.ArrayList;
public class Main {
public static int getSurvivingIndex(int n, int k) {
ArrayList<Integer> soldiers = new ArrayList<>();
    for (int i = 1; i <= n; i++) {
        soldiers.add(i);
    }
		// 죽여야하는 병사의 index
    int idx = 0;

    while (soldiers.size()>1) {
				// 병사가 죽으면 index도 줄어드므로 (idx + k) 가 아닌 (idx + k - 1)을 써야한다. 
				// soldiers ArrayList의 끝까지 돌면 idx가 초기화 되어야하니 soldiers.size()로 나눈 나머지를 idx에 다시 대입해준다.
        idx = (idx + k - 1) % soldiers.size();
		    
				System.out.println(soldiers.remove(idx)+"번 군사가 죽었습니다.");
		}
    return numList.get(0);
}

public static void main(String[] args){
        System.out.println("장군은 " + getSurvivingIndex(20, 5) + "번 자리에 서있으면 됩니다.");
    }
}
```

```java

```

# 5. HashMap

⇒ 값에 해당하는 원소와 key를 같이 저장한다.

Key, Value쌍!

**키로는 `String`을 쓰는 것이 가장 일반적이다.**

```java
import java.util.ArrayList;
import java.util.HashMap;

public class Player {
    public static void main(String[] args) {
        ArrayList<Players> arrayList = new ArrayList<>();
        arrayList.add(new Players("케빈데브라위너"));
        arrayList.add(new Players("손흥민"));
        arrayList.add(new Players("베실바"));

        HashMap<String, Players> player = new HashMap<>();
        player.put("황의조", new Players("황의조"));
        player.put("조현우", new Players("조현우"));
    }
}
```

**ArrayList**는 값을 지우면 index가 변하는 것에 비해
**Hashmap은** 값을 지워도 key-value 쌍이 그대로 남아있으므로, 관리가 편하다!

- **`HashMap은 add 대신에 put을 쓴다`**
- HashMap은 key 하나당 value 하나만 사용할 수 있다.
- **덮어쓴다면 맨 마지막에 저장한 값만 저장된다**

**HashMap의 key들을 모두 받아오려면?**

- `**players.keySet()`**  메소드를 사용하면 된다!

   ⇒ HashMap의 모든 key를 담고 있는 **`Set`**을 리턴해준다.

> **출력을 원한다면!**
> 

```java
for (String key : Players.keySet()) {
		System.out.println(Players.get(key));
}
```

### **(심화) HashMap의 동작 원리**

**HashMap**의 key는 **'hashcode'**라는 것으로 관리된다. 

- 이 hashcode는 모든 클래스의 인스턴스가 가진 고유한 값인데, 인스턴스마다 다르기 때문에 
**HashMap**이 key를 구분하는 값으로 사용된다.
- 여러 인스턴스가 같은 hashcode를 가질 수 있으며, 
이 경우 **HashMap**에선  **key.equals(anotherKey)** 메소드로 구분한다
일반적인 클래스는 인스턴스 생성시 **hashcode** 값이 결정된다.
- **즉, 같은 정보를 담고 있는 두 인스턴스가 서로 다른 hashcode를 가질 수 있다**
- 그런데 **String**은 서로 다른 인스턴스라도 안의 내용이 같으면 같은 hashcode를 갖는다.
- 그렇기 때문에 **HashMap**의 key로서 **String**이 매우 적합하다!
Key는 **실제 인스턴스보다는 안에 담긴 의미, 내용으로 구분하는 것이 좋기 때문**