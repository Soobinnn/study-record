# 컨테이너에 등록된 모든 빈 조회


```java
class ApplicationContextInfoText {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("모든 빈 출력하기")
    void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();

        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name= "+ beanDefinitionName + " object = "+ bean);
        }
    }

    @Test
    @DisplayName("애플리케이션 빈 출력하기")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();

        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            // 스프링 내부의 빈이 아닌 개발자가 등록한 빈
            // Role ROLE_APPLICATION : 직접 등록한 애플리케이션 빈
            // Role ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈
            if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                Object bean = ac.getBean(beanDefinitionName);
                System.out.println("name= "+ beanDefinitionName + " object = "+ bean);
            }
        }
    }
}

```

- 모든 빈 출력하기

    - 실행하면 스프링에 등록된 모든 빈 정보를 출력할 수 있다.

    - ac.getBeanDefinitionNames() : 스프링에 등록된 모든 빈 이름을 조회한다.

    - ac.getBean() : 빈 이름으로 빈 객체(인스턴스)를 조회한다.

- 애플리케이션 빈 출력

    - 스프링이 내부에서 사용하는 빈은 제외하고, 내가 등록한 빈만 출력해보자.

    - 스프링이 내부에서 사용하는 빈은 getRole()로 구분할 수 있다.

        - ROLE_APPLICATION : 일반적으로 사용자가 정의한 빈

        - ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈

