- Inversion of Control
- Dependency Inversion Priciple
- Dependency Injection

# Bean의 생명주기
1. 스프링 IoC 컨테이너 생성
2. 스프링 빈 생성
    - Configuration Metadata => Bean Definition
        - Annotation 
        - Java Config
        - Xml
    - POJO (Plain Old Java Object)
3. 의존관계 주입
4. 초기화 콜백 메소드 호출
5. 사용
6. 소멸 전 콜백 메소드 호출
7. 스프링 종료
 


# Bean Scope
- request
    - HTTP 요청이 들어오고 나갈 때까지 유지되는 스코프
    - 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고 관리
- session
    - HTTP 세션과 동일한 생명주기를 갖는 스코프
- application
    - 서블릿 컨텍스트와 동일한 생명주기를 갖는 스코프
- websocket
    - 웹 소켓과 동일한 생명 주기를 갖는 스코프


## Bean 생명주기 관리자
- Spring Container
    - Bean 생명주기 관련 callback 메서드를 호출하여 관리
        - Bean 객체의 생성, 초기화, 소멸
- 지원
    - Spring 인터페이스
    - JSR-250 어노테이션
    - @Bean 어노테이션 속성


## Bean Creation 생명주기
1. 스프링이 빈 객체를 인스턴스화 한다.
2. 의존 관계를 주입한다.
3. `BeanNameAware.setBeanName()` 호출
    - setBeanName의 파라미터로 넘어온 문자열 값으로 빈의 이름을 설정
4. `BeanFactoryAware.setBeanFactory()` 호출
    - BeanFactory 객체를 주입하기 위함
5. `ApplicationContextAware.setApplicationContext()` 호출
    - ApplicationContext 객체를 주입하기 위함
6. `BeanPostProcessor.postProcessBeforeInitializtion()` 호출
7. 다음 순서대로 호출
    1. @PostConstruct가 붙은 메서드
    2. InitializingBean.afterPropertiesSet()
    3. @Bean의 initMethod로 지정한 메서드
8. `BeanPostProcessor.postProcessAfterInitialization()` 호출

## Bean Destruction 생명주기
1. 스프링 IoC 컨테이너가 종료된다
2. 다음 순서대로 호출
    1. @PreDestroy가 붙은 메서드
    2. DisposableBean.destroy()
    3. @Bean의 destroyMethod로 지정한 메서드


