#  관심사의 분리

- 애플리케이션을 하나의 공연이라 생각해보자. 각각의 인터페이스를 배역 (배우 역할)이라 생각하자. 그런데 실제 배역에 맞는 배우를 선택하는 것은 누가 하는가?

- 로미오와 줄리엣 공연을 하면 로미오 역할을 누가 할지 줄리엣 역할을 누가 할지는 배우들이 정하는게 아니다. 

이전 코드는 마치 로미오 역할(인터페이스)을 하는 레오나르도 디카프리오(구현체, 배우)가 줄리엣 역할(인터페이스)을 하는 여자 주인공(구현체, 배우)를 직접 초빙하는 것과 같다. 

디카프리오는 공연도 해야하고 동시에 여자 주인공도 공연에 직접 초빙해야 하는 "다양한 책임"을 가지고 있다.

** 관심사를 분리하자*

- 배우는 본인의 역할인 배역을 수행하는 것에만 집중해야 한다.

- 디카프리오는 어떤 여자 주인공이 선택되더라도 똑같이 공연을 할 수 있어야 한다.

- 공연을 구성하고, 담당 배우를 섭외하고, 역할에 맞는 배우를 지정하는 책임을 담당하는 별도의 `공연 기획자`가 나올 시점이다.

- 공연 기획자를 만들고, 배우와 공연 기획자의 책임을 확실히 분리하자.


## AppConfig 등장

- 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, `구현 객체를 생성`하고, 연결 하는 책임을 가지는 별도의 설정 클래스를 만들자

```java
public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
    }
}

```

- AppConfig는 애플리케이션의 실제 동작에 필요한 **구현 객체를 생성** 한다.

    - MebmerServiceImpl

    - MemoryMemberRepository

    - OrderServiceImpl

    - FixDiscountImpl

- AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 `생성자를 통해서 주입(연결)` 해준다.

    - MemberServiceImpl -> MemoryMemberRepository

    - OrderServiceImpl -> MemoryMemberRepository, FixDiscountPolicy


* MemberServiceImpl - 생성자 주입

```java
public class MemberServiceImpl implements MemberService{

    private final MemberRepository memberRepository;

    // 생성자 주입
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}

```

- 설계 변경으로 `MemberServiceImpl`은 `MemoryMemberRepository`를 의존하지 않는다.

- 단지 `MemberRepository` 인터페이스만 의존한다.

- `MemberServiceImpl` 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.

- `MemberServiceImpl`의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(AppCOnfig)에서 결정된다.

- `MemberServiceImpl`은 이제부터 `의존관계에 대한 고민은 외부`에 맡기고 `실행에만 집중`하면 된다.

** 클래스 다이어그램

![chap16-1](./image/chap16-1.png)

- 객체의 생성과 연결은 `AppConfig`가 담당한다.

- DIP 완성: MemberServiceImpl은 MemberRepository 인 추상에만 의존하면 된다.

    -> 이제 구체 클래스를 몰라도 된다.

    \* 관심사의 분리: 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리되었다.


- AppConfig 객체는 memoryMemberRepository 객체를 생성하고 그 참조값을 memberServiceImpl을 생성하면서 생성자로 전달한다.

- 클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 해서 DI (Dependency Injection) 우리말로 의존관계 주입 또는 의존성 주입이라 한다.

### OrderServiceImpl - 생성자 주입

```java
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

- 설계 변경으로 `OrderServiceImpl`은 `FixDiscountPolicy`를 의존하지 않는다.

- 단지 DiscountPolicy 인터페이스만 의존한다

- OrderServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.

- OrderServiceImpl의 생성자를 통해 어떤 구현 객체를 주입할지는 오직 외부(AppcConfig)에서 결정한다.

- OrderServiceImpl은 이제부터 실행에만 집중하면 된다.

- OrderServiceImpl 에는 MemoryMemberRepository, FixDiscountPolicy 객체의 의존관계가 주입된다.

### AppConfig 실행