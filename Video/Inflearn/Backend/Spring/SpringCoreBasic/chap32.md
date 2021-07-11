# 싱글톤 패턴

클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴

그래서 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 한다.

    - private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 한다.

```java
public class SingletonService {

    // 1. static 영역에 객체를 딱 1개 만 생성해둔다.
    private static final SingletonService instance = new SingletonService();

    // 2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
    public static SingletonService getInstance() {
        return instance;
    }

    // 3. 생성자를 private로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
    private SingletonService() {}

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
    
}
```

\* 참고 : 싱글톤 패턴을 구현하는 방법은 여러가지가 있다. 여기서는 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 선택했다.

싱글톤 패턴을 적용하면 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다. 

하지만, 싱글톤 패턴은 다음과 같은 문제점들을 가지고 있다.

### 싱글톤 패턴 문제점

1. 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.

2. 의존관계상 클라이언트가 구체 클래스에 의존한다. 

    -> DIP 위반

3. 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.

4. 테스트하기 어렵다.

5. 내부 속성을 변경하거나 초기화 하기 어렵다.

6. private 생성자로 자식 클래스를 만드릭 어렵다.

7. 결론적으로 유연성이 떨어진다.

8. 안티패턴으로 불리기도 한다.