# AppConfig 리팩터링

현재 AppConfig를 보면 중복이 있고, 역할에 따른 구현이 잘 안보인다.
이 그림을 보면 클라이언트가 주문할 때를 생각해보면 주문 서비스 역할, 할인 정책 역할, 회원 서비스 역할이 있었다.
그래서 역할과 구현을 분리 해 한 그림에 보고 싶은데,
설정 (구성) 정보에는 그게 한 눈에 보이는게 굉장히 중요하지만 현재 AppConfig 에는 그게 전혀 보이지 않는다.
따라서 역할을 드러내게 하는게 굉장히 중요하다.

현재 MemberRepository 라는 역할도 보여야 하지만 전혀 보이지 않는다.

- **기대하는 그림**
![image](https://github.com/al1kite/spring-wiki/assets/102217402/935e07e4-15ba-4fa8-bd22-4d6ec20db55c)

```java
public class AppConfig {
 public MemberService memberService() {
 return new MemberServiceImpl(memberRepository());
 }
 public OrderService orderService() {
 return new OrderServiceImpl(
 memberRepository(),
 discountPolicy());
 }
 public MemberRepository memberRepository() {
 return new MemoryMemberRepository();
 }
 public DiscountPolicy discountPolicy() {
 return new FixDiscountPolicy();
 }
}
```
이렇게 작성 시 각각의 역할까지 다 드러난다.
어떤 큰 장점이 있냐면 메소드명만 봐도 역할이 다 드러난다. 그리고 현재 나의 애플리케이션에서는 MemberService에 대한 구현은 MemberServiceImpl을 쓸 거라는 것과
또한 MemberRepository에 대한 것은 MemoryMemberRopitory 를 할 것이라는 것, 혹시 Member가 아닌 DB 로 변경 희망 시 저 코드만 수정해주면 된다.

따라서 설계에 대한 구현이 이 구성 정보에 그대로 드러나게 된다.
역할이 나오고 역할에 대한 구현이 어떻게 나오는지가 한 눈에 드러나게 된다.

- new MemoryMemberRepository() 이 부분이 중복 제거되었다. 이제 MemoryMemberRepository 를
다른 구현체로 변경할 때 한 부분만 변경하면 된다.
- AppConfig 를 보면 역할과 구현 클래스가 한눈에 들어온다. 애플리케이션 전체 구성이 어떻게 되어있는지 빠르게 파악할 수 있다.


