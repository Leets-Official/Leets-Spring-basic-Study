# 3강 - 스프링 핵심 원리 이해2 - 객체 지향 원리 적용

# 📔  새로운 할인 정책 개발

---

역할과 구현을 분리해 자유롭게 구현 객체를 조립할 수 있도록 함 ⇒ 나중에 변경 사항이 있더라도 그 부분만 바꾸면 됨

역할과 구현 분리 : OK

다형성 활용, 인터페이스와 구현 객체 분리 : OK

OCP, DIP같은 객체 지향 설계 원칙을 충실히 준수 : X

<aside>
💡 ⇒ OrderServiceImpl는 추상(인터페이스) 뿐만 아니라 구체(구현) 클래스에도 의존하고 있다

- 인터페이스 의존 : DiscountPolicy
- 구체(구현) 클래스 : FixDiscountPolicy, RateDiscountPolicy

private DiscountPolicy discountPolicy = new FixDiscountPolicy();을

private DiscountPolicy discountPolicy = new RateDiscountPolicy();으로 바꾸어지니 클라이언트에 코드에 영향 ⇒ OCP 위반

</aside>

해결 방법

- DIP를 위반하지 않도록 인터페이스에만 의존하도록 의존 관계를 변경

# 📔  AppConfig

---

애플리케이션의 전체 동작 방식을 구성하기 위해 구현 객체를 생성하고 연결하는 책임을 가지는 별도 설정 클래스

AppConfig class에서

```jsx
public MemberService memberService(){
	return new MemberServiceImpl(new MemoryMemberRepository());
}
```

생성자 주입을 해주게 되면 DIP를 위반하지 않을 수 있음

수정 이후

- MemoryServiceImpl은 MemoryMemberRepository를 의존하지 않고, MemberRepository 인터페이스를 의존한다
- MemberServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 주입될지는 알 수 없다
- MemberServiceImpl의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(AppConfig)에서 결정된다

관심사의 분리 : 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리됨

클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 해서 DI(Dependency injection) 의존관계 주입/의존성 주입이라고 함

# 📔  IoC, DI, container 설명

---

IoC : 내가 호출하는게 아닌 프레임워크 등이 대신 호출 해주는 것

- AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다. 프로그램의 제어 흐름은 이제 AppConfig가 가져감

프레임워크 vs 라이브러리

프레임워크 : 내가 작성한 코드를 제어하고, 대신 실행하면 프레임워크 (JUnit)

라이브러리 : 내가 작성한 코드가 직접 제어의 흐름을 담당

DI(의존관계 주입)

- 정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계 둘을 분리해서 생각해야 함

의존성 관계 주입 : 애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것

- 객체 인스턴스를 생성하고, 그 참조값을 저달해서 연결됨
- 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다
- 의존관계 주입을 사용하면 정적인 클래스 의존 관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경 가능

IoC 컨테이너, DI 컨테이너

- AppConfig처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 or ***DI 컨테이너***(의존관계 주입을 초점을 맞추어 주로 DI컨테이너라고 부름)라고 함