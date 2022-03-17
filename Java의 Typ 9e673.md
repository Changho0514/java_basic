# Java의 Type

# 기본형 (Primitive Type) vs 참조형(Reference Type)

## 기본형

## 참조형

- **int
boolean
char
double
...**

- **Person
String
int[]
...**

> **기본형 ⇒ 변수가 값 자체를 보관
참조형 ⇒ 변수가 값이 보관되어 있는 영역을 가리킴**
> 

```java
int a = 3;
int b = a;
```

```java
System.out.println(a);*// 3 출력*
System.out.println(b);*// 3 출력*
a = 4;
System.out.println(a);*// 4 출력*
System.out.println(b);*// 3 출력*
b = 7;
System.out.println(a);*// 4 출력*
System.out.println(b);*// 7 출력*
```

먼저 **`a`**에 **`3`**이 보관되고, **`b`**에는 **`a`**의 값인 **`3`**이 복사돼서 보관된다. 
그 다음 **`a = 4`**를 하면 **`a`**만 바뀌고`**b**`는 그대로 **`3`** 이다.
마찬가지로 **`b = 7`**을 하면 **`b`**만 바뀌고 **`a`**는 그대로 **`4`** 로 남아있다.

### 반면에 참조형은

**실제 값은 메모리의 어딘가에 저장되어 있고, 변수는 그 영역을 가리키는 역할을 한다.**

```java
Person p1, p2;
p1 = new Person("최창호", 28);
p2 = p1;

p2.setName("손흥민");
System.out.println(p1.getName());
System.out.println(p2.getName());

손흥민
손흥민
```

**`p1`**은 **`"최창호"`**라는 이름을 가진 **`Person`** 인스턴스가 저장되어 있는 영역을 가리킨다.
**`p2 = p1`**은 **`p2`**에게 같은 영역을 가리키라는 명령이다.
**`p2.setName("손흥민")`**를 하면 그 영역에 있는 인스턴스의 **`name`**은 
**`"손흥민"`**로 바뀐다. **`p1,`** **`p2`** 모두 같은 영역을 가리키고 있으니까 
두 출력 값은 모두 **`"손흥민"`** 이 나온다.

# 그럼 String은 기본형일까 참조형일까?

⇒ **Java String은 조금 독특한 녀석이다...!**
Java의 String은 **2가지 방식으로 생성이 가능**하다.

```java
String s1 = "hello";
String s2 = "hello";

String ss1 = new String("hello");
String ss2 = new String("hello");

```

1. 바로 값을 넣는 방식
2. new를 통한 생성자 방식

이때, 차이점은 그냥 **`String s1 = "hello"`**라고 선언한 s1과 s2는 **`==`**을 통해 물어보면 true라 출력하고 **`String ss1 = new String("hello")`**라고 선언한 ss1과 ss2는 **`==`**을 통해 물어보면 false라 출력한다.

```java
        System.out.println("String Test");

        String s1 = "hello";
        String s2 = "hello";

        String ss1 = new String("hello");
        String ss2 = new String("hello");

        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
        System.out.println(ss1 == ss2);
        System.out.println(ss1.equals(ss2));

```

위의 코드를 실행해보시면, 결과 값으로 아래와 같이 나온다.

```java
String Test
true
true
false
true

```

즉, **기본형 변수 이기도 하면서, 참조형 변수 이기도 한 것 같다.**

( 정말 팩트로만 따진다면 사실상 기본형 변수가 아니지만 **Java에서 기본형 변수처럼 사용하도록 만들어준다**.)
**그냥 값을 넣었을 때와 new 를 이용한 생성자 생성 방법은 조금 다르기도 하니까 알아두면 좋을 듯 하다!**

# Null

자바에서는 '비어있음'이 **`null`**이라는 값으로 표현된다. 

<aside>
✅ **단, `null`은 참조형 변수(Reference Type)만 가질 수 있는 값이다!!**

</aside>

만약 **`null`**을 보관하고 있는 변수의 메소드를 호출하려고 하면 **`NullPointerException`**이라는 오류가 난다

# **대처법**

아래 코드는 오류가 날 것이다. 
**`people[1]`**과 **`people[4]`**는 **`null`**이기 때문에 **`p.getName()`**을 할 수가 없다

```java
Person[] people = new Person[5];
people[0] = new Person("김신의", 28);
people[2] = new Person("문종모", 26);
people[3] = new Person("서혜린", 21);

for (int i = 0; i < people.length; i++) {
    Person p = people[i];
    System.out.println(p.getName());
}
```

<aside>
✅ **이 문제를 해결하기 위해서는 `p`가 `null`인지 먼저 확인을 해주면 된다.**

</aside>

```java
Person[] people = new Person[5];
people[0] = new Person("김신의", 28);
people[2] = new Person("문종모", 26);
people[3] = new Person("서혜린", 21);

for (int i = 0; i < people.length; i++) {
    Person p = people[i];
    if (p != null) {
        System.out.println(p.getName());
    } else {
        System.out.println(i + "번 자리는 비었습니다.");
    }
}
```

# Short-circuit

### **And 연산 (&&)**

```java
boolean newBoolean = m1() && m2() && m3();

```

**`newBoolean`**이 **`true`**가 되기 위해서는 **`m1()`**, **`m2()`**, **`m3()`**가 모두 **`true`**를 리턴해야 한다 
따라서 **`m1()`**이 **`false`**를 리턴하면 **`m2()`**와 **`m3()`**의 결과와 상관 없이 **`newBoolean`**은 **`false`**가 된다.

실제로 **자바는 효율성을 위해서 `m1()`**이 **`false`**를 리턴하면 `**m2()`와 `m3()`를 실행하지 않는**다. 

식의 결과값이 이미 결정된 경우 미리 멈추는 것을 **'숏서킷 연산(Short-circuit evaluation)'**이라고 한다.

만약 **`m1()`**이 **`true`**를 리턴하는데, **`m2()`**가 **`false`**를 리턴하면 **`m3()`**는 실행되지 않는다.

### Or 연산(**`||`**)

Or 연산도 비슷하게 동작한다.

```java
boolean newBoolean = m1() || m2() || m3();

```

**`newBoolean`**이 **`false`**이기 위해서는 **`m1()`**, **`m2()`**, **`m3()`**의 리턴값이 모두 **`false`**이어야 한다. 
따라서 **`m1()`**이 **`true`**를 리턴하면 **`m2()`**와 **`m3()`**는 실행되지 않고 **`newBoolean`**은 **`true`**가 된다.

## 자료형의 기본값

```java
int[] a = new int[2];
System.out.println(a[0]);
```

값이 뭐가 나올까?

> **`int`** 배열을 선언하고 값을 넣어주지 않았다면  **해당 자료형의 기본값**이 사용된다. 
**`int`**의 기본값은 **`0`**이기 때문에 **`0`**이 출력된다.
> 

```java
Person[] people = new Person[2];
System.out.println(people[0]);
```

> **`Person`**은 클래스이기 때문에 **`people[0]`**은 **참조형(Reference Type)이다**. 
참조형의 기본 값은 **`null` 이다.**
> 

```java
Person person = new Person();
people[0] = person;
person.age = a[0];
a[0] = 26;
System.out.println(person.age);
```

> **`person.age`**와 **`a[0]`**은 기본 자료형 **`int`이다**. 
따라서 **`a[0]`**의 값이 **`person.age`**에 **복사**되는 것!! 
그 후에 **`a[0] = 26;`**을 하여도 **`person.age`**에는 그 전에 복사된 값 **`0`**이 남아 있다.
> 

# Final

<aside>
✅ **변수를 정의할 때 `final`을 써주면, 그 변수는 '상수'가 된다.**

</aside>

```java
final double pi = 3.141592;
pi = 2.54;

```

이 코드는 오류가 나온다. **`final`**로 **정의된 변수의 값을 바꿀 수 없기 때문에**.
**`π`**(**`pi`**)처럼 바꾸지 않는, 바꾸지 말아야 할 값이 있으면 **`final`**을 쓰면 된다.

```java
public class Person {
private String name;

		public Person(String name) {
		    this.name = name;
		}
		
		public void setName(String name) {
		    this.name = name;
		}
		
		public String getName() {
		    return this.name;
		}
		
		public static void main(String[] args) {
		    final Person p1 = new Person("최창호");
		    p1.setName("손흥민");
		    System.out.println(p1.getName());
		}
}
```

이 코드는 문제 없이 실행된다. **`p`**을 **`final`**로 정의했는데 왜 그럴까? 
**`final`**은 **`p1`**에 해당하는 것이지, **`p1`**의 인스턴스 변수에 해당하는 것이 아니기 때문!!!
 따라서 **`p1`**의 이름을 못 바꾸도록 만들고 싶으면 **`Person`**클래스 **내에서** 
`**name`을 `final`로 정의해주면 된다**.

# 인스턴스 변수 vs 클래스 변수

지금까지는 인스턴스에 해당되는 '**인스턴스 변수'**와 '**인스턴스 메소드**'를 사용했다.

```java
Person p1 = new Person("최창호", 25, 50000);
BankAccount a1 = new BankAccount(100000);

a1.setOwner(p1);// a1에 대한 인스턴스 메소드
a1.withdraw(20000);// a1에 대한 인스턴스 메소드
```

하지만 인스턴스에 해당되지 않고, **`클래스에 해당되는 '클래스 변수'`**도 존재한다.
이 ***둘의 차이점을 정확히 이해해야, 좋은 객체 지향 코드를 작성할 수 있다***.

### 1. 인스턴스 변수

현재는 **`Person`** 클래스에 **`count`**라는 인스턴스 변수가 있을때 
**`count`**는 현재 총 몇 개의 **`Person`** 인스턴스가 있는지 보관한다.

```java
public class Person {
    int count;
}

```

그런데 새로운 **`Person`** 인스턴스를 생성할 때마다 각 인스턴스의 **`count`** 변수를 하니 굉장히 번거롭다.

```java
public static void main(String[] args) {
    Person p1 = new Person();
    p1.count++;

    Person p2 = new Person();
    p1.count++;
    p2.count = p1.count;

    Person p3 = new Person();
    p1.count++;
    p2.count++;
    p3.count = p2.count;

    Person p4 = new Person();
    p1.count++;
    p2.count++;
    p3.count++;
    p4.count = p3.count;

    System.out.println(p1.count);
    System.out.println(p2.count);
    System.out.println(p3.count);
    System.out.println(p4.count);
}

```

```
4
4
4
4
```

### 2. 클래스 변수

**이런 문제를 해결할 수 있는 게 바로 클래스 변수이다.** 
**클래스 변수를 정의하기 위해서는 `static`이라는 키워드를 붙여주면 된다.**

```java
public class Person {
    static int count;
}

```

**이제 `count`는 특정 인스턴스에 해당되는 게 아니라, `Person` 클래스 전체에 해당되는 것**. 
따라서 **`count`**를 부를 때는 대문자로 쓴 클래스 이름을 사용해서 **`Person.count`**를 써주면 된다.

```java
public static void main(String[] args) {
    Person p1 = new Person();
    Person.count++;

    Person p2 = new Person();
    Person.count++;

    Person p3 = new Person();
    Person.count++;

    Person p4 = new Person();
    Person.count++;

    System.out.println(Person.count);
}

```

```
4

```

많이 깔끔해지만 아직도 반복적인 코드가 많다. 
**반복적인 코드는 안 좋은 것이기 때문에 개선시킬 방법이 필요하겠다!**

> 우리는 지금 새로운 인스턴스를 생성할 때마다 **`Person.count`**를 **`1`**씩 늘려주고 있다.
그러면 아예 `**생성자에 이 줄을 넣어버리자!!**`
> 

```java
public class Person {
    static int count;

    public Person() {
        count++;
    }
}

```

```java
public static void main(String[] args) {
    Person p1 = new Person();
    Person p2 = new Person();
    Person p3 = new Person();
    Person p4 = new Person();

    System.out.println(Person.count);
}

```

```
4

```

훨씬 깔끔하다. **이런 식으로 변수가 클래스 자체에 해당될 때에는 `static`을 써서 클래스 변수로 만들어준다**

### 3. 상수

**`final`**을 공부할 때 상수를 보긴 했지만, 상수를 더 상수답게 쓰려면 **`static`**과 함께 쓰는 것이 좋다.
상수는 인스턴스에 해당되는 것이 아니며, **여러 복사본 대신 한 값만** 저장해두는 것이 맞기 때문에!!
**상수 이름은 보통 모두 대문자로 쓰고, 단어가 여러 개인 경우 `_`로 구분 짓는다.**

```java
public class JavaConstants {
    public static final double PI = 3.141592653589793;
    public static final double EULERS_NUMBER = 2.718281828459045;
    public static final String THIS_IS_HOW_TO_NAME_CONSTANT_VARIABLE = "Hello";

    public static void main(String[] args) {
        System.out.println(JavaConstants.PI + JavaConstants.EULERS_NUMBER);
    }
}

```

```
5.859874482048838
```

# 인스턴스 메소드 vs 클래스 메소드

> **인스턴스 메소드'는 인스턴스에 속한 것이기 때문에, 반드시 인스턴스를 생성해야 사용할 수 있다.**
하지만 '클래스 메소드'는 클래스에 속한 것이기 때문에, 인스턴스를 생성하지 않고도 바로 실행할 수 있다.
> 

### 1. main

사실 우리가 가장 먼저 접한 '클래스 메소드'는 바로 **`main`** 메소드이다.

```java
public static void main(String[] args) {
    ...
}

```

**`main`**은 자바 프로그램의 시작점이다. 첫 번째로 실행되는 코드이니, ***어떤 인스턴스도 생성되어 있지 않겠지?***
**`main`** 메소드 역시 인스턴스를 생성하지 않고 실행하는 **`'클래스 메소드'`**이다.

> 클래스 변수를 정의하기 위해서는 **`static`**이라는 키워드를 사용한 것처럼 
클래스 메소드도 동일하게 **`static`**이라는 키워드로 정의할 수 있다. 
**`main`** 앞에 **`static`** 키워드가 있는 걸 보니, 클래스 메소드가 확실하군!
>