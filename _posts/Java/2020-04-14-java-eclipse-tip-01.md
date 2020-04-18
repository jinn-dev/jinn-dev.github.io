---
title: "이클립스 단축키 - Assign statement to Local Variable"
categories:
  - Eclipse
last_modified_at: 2020-04-14T22s:00:00+09:00
toc_sticky: false
toc: false
comments: true
---


다른 클래스의 메소드를 쓰면 메소드 반환값을 담을 변수가 필요하다.       

이 때 단축키 하나로 지정해줄 수 있는걸 지금 발견했다.     



**1. MemberRepository**

```java 
@Repository
public class MemberRepository {

	@PersistenceContext
	private EntityManager em;

	public Long save(Member member) {
		em.persist(member);
		return member.getId();
	}
}
```

​        

MemberRepository의 save 메소드는 Long 반환 타입을 갖는다.

​       

**2. MemberRepositoryTest**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {

	@Autowired MemberRepository memberRepository;
	
	@Test
	public void testMember() throws Exception {
		//given
		Member member = new Member();
		member.setUsername("memberA");
		memberRepository.save(member);
	}
}
```

`memberRepository.save(member);`      

해당 라인은 반환 받을 변수는 없는 상태이다.    

​    

 `Ctrl+2` 누르고 바로 `L` 을 또 누르면    

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {

	@Autowired MemberRepository memberRepository;
	
	@Test
	public void testMember() throws Exception {
		//given
		Member member = new Member();
		member.setUsername("memberA");
		Long save = memberRepository.save(member);
	}
}
```

`Long save = memberRepository.save(member);`

위와 같이 변경된다.