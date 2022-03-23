# Comparable Interface

ArrayList를 정렬하려면 Collections.sort(ArrayList) 하면 된다.

만약 PersonList 가 **name, age 변수를 모두 갖고있다면?
어떤 기준으로 정렬될까?**

[Person.java](http://Person.java) 클래스로 가서

```java
public class Person implements Comparable<Person>{
    public final String name;
    public final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return name + ": " + age + "세";
    }

    @Override
		//현재 나이(age) 대비, instance 나이(o.age)와의 비교
    public int compareTo(Person o) {
        return age - o.age;
    }
}

```

### Main.java

```java
import java.util.ArrayList;
import java.util.Collections;

public class Main<personList> {
    public static void main(String[] args) {
        ArrayList<Person> personList = new ArrayList<>();
        personList.add(new Person("Charlie", 25));
        personList.add(new Person("Ben", 35));
        personList.add(new Person("Alex", 32));
        personList.add(new Person("Daniel", 41));
    }

    Collections.sort(personList);
}

```

[Charlie: 25세, Alex: 32세, Ben: 35세, Daniel: 41세] 처럼 오름차순으로 정렬이 된다!

**만일 이름순으로 정렬하고 싶다면?**

```java
//현재 나이(age) 대비, instance 나이(o.age)와의 비교
    public int compareTo(Person o) {
        return age - o.age;
```

이부분을

```java
//이름순으로 비교 
    public int compareTo(Person o) {
        return name.compareTo(o.name);
```