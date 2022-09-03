# 순수 JPA 페이징과 정렬

### JPA에서 페이징을 어떻게 할 것인가?

다음 조건으로 페이징과 정렬을 사용하는 예제 코드를 보자.

- 검색 조건: 나이가 10살
- 정렬 조건: 이름으로 내림차순
- 페이징 조건: 첫 번째 페이지, 페이지당 보여줄 데이터는 3건

### JPA 페이징 리포지토리 코드

```java
public List<Member> findByPage(int age, int offset, int limit) {
		return em.createQuery("select m from Member m where m.age = :age order by m.username desc")
				.setParameter("age", age)
				.setFirstResult(offset)   //어디서부터 가져올지
				.setMaxResults(limit)     //얼마나 가져올지
				.getResultList();
		}		
public long totalCount(int age) {
		return em.createQuery("select count(m) from Member m where m.age = :age", Long.class)
				.setParameter("age", age)
				.getSingleResult();
}
```

![Untitled](%E1%84%89%E1%85%AE%E1%86%AB%E1%84%89%E1%85%AE%20JPA%20%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5%E1%86%BC%E1%84%80%E1%85%AA%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%A7%E1%86%AF%2002814f4d50074c61bdc4156dca9aed4e/Untitled.png)

### JPA 페이징 테스트 코드

```java
@Test
public void paging() {
    //given
    memberJpaRepository.save(new Member("member1", 10));
    memberJpaRepository.save(new Member("member2", 10));
    memberJpaRepository.save(new Member("member3", 10));
    memberJpaRepository.save(new Member("member4", 10));
    memberJpaRepository.save(new Member("member5", 10));

    int age = 10;
    int offset = 0;
    int limit = 3;

    //when
    List<Member> members = memberJpaRepository.findByPage(age, offset, limit);
    long totalCount = memberJpaRepository.totalCount(age);

		
		//페이지 계산 공식 적용...
		// totalPage = totalCount / size ...
		// 마지막 페이지 ...
		// 최초 페이지 ..
    //then
		assertThat(members.size()).isEqualTo(3);
		assertThat(totalCount).isEqualTo(5);
}
```

이건 DB가 바뀌어도 상관없다.

JPA는 방언을 기반으로 실행되기 때문에 DB가 바뀌어도 JPQL을 제대로 내보내 줄 수 있다.