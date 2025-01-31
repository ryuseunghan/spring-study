## 1장

### 스프링이란?

보통 스프링은 자바 애플리케이션 개발을 위한 **경량** 프레임워크로 설명되지만

1. 스프링은 웹 애플리케이션 개발에만 사용된 아파치 스트럿츠와 같은 다른 여러 프레임워크와는 다르게 스프링은 **어떤 형태의 자바** 애플리케이션이라도 개발할 수 있게 해준다.
2. **경량**이란 부분은 클래스 개수나 배포 모듈의 크기를 뜻하는 것이 아니라 스프링 철학의 원칙인 최소한의 영향을 준다는 것을 의미한다. 스프링은 스프링 코어를 활용할 때 기존 애플리케이션 코드를 거의 바꾸지 않아도 된다는 점에서 매우 가볍다.
   - 스프링 코어 외에 데이터 액세스와 같은 대부분의 추가 스프링 컴포넌트는 스프링 프레임워크와 밀접하게 결합된다.

### 제어 역전(Inversion of Control, IoC) & 의존성 주입(Dependency Injection, DI)

IoC는 컴포넌트 간의 의존성 생성과 관리를 외부에서 수행하는 기법이며, 스프링 프레임워크의 코어는 IoC 원칙에 기반을 두고 있다.

예를 들어, `Foo` 클래스가 `Bar` 클래스의 인스턴스에 의존해 특정 작업을 수행한다고 가정해보자.

- **전통적 방식:** `Foo`는 `new` 연산자를 사용해 `Bar`의 인스턴스를 생성하거나 팩터리 클래스를 통해 인스턴스를 생성한다.
- **IoC 접근 방식:** 런타임에 외부 프로세스에 의해 `Bar` 인스턴스가 `Foo`에게 제공된다.

그리고, 이러한 방식을 의존성 주입(DI)라는 용어로 부른다.

> 제어 역전이 꼭 의존성 주입을 의미하지는 않지만, 스프링에서는 두 용어를 혼용하더라도 문제가 없다.

스프링의 DI 구현체는 자바빈과 인터페이스의 두 가지 핵심적인 자바 개념을 기반으로 한다. 스프링을 DI 제공자로 사용하면 애플리케이션의 의존성 구성을 유연하게 정의할 수 있다. 자바빈은 다양한 방식으로 설정할 수 있는 자바 리소스 표준 메커니즘을 제공한다.

인터페이스와 DI는 서로에게 도움이 되는 기술이다. 애플리케이션을 인터페이스로 명확하게 설계하고 코딩하면 유연한 애플리케이션을 만들 수 있지만, 이 인터페이스를 서로 연결하는 작업은 개발자에게 부담이다. 하지만, DI를 사용하면 추가적인 코드가 거의 필요하지 않다.

DI와 관련해 스프링은 프레임워크보다는 컨테이너 역할을 한다. 스프링은 애플리케이션 내에서 의존 관계를 가진 모든 클래스의 인스턴스를 제공하지만 이를 위햐서는 특정한 방법을 강제하지 않고 클래스가 자바빈 명명 규칙을 따르기만 하면 된다.

### 의존성 주입 발전 과정

1. 접착 코드 감소
   - 컴포넌트를 서로 연결할 때 작성해야 하는 코드의 양 감소
2. 애플리케이션 구성 단순화
   - Injector 자체에 대한 의존성 요구 가능
   - PostgreSQL DAO 컴포넌트 -> 오라클로 변경할 때, 비즈니즈 객체가 PostgreSQL 대신 오라클 구현체를 사용하도록 의존성을 간단하게 재구성할 수 있다.
3. 단일 저장소에서 공통 의존성 관리
   - 공통적인 의존성에 대한 정보가 모두 단일 저장소에 저장
4. 테스트 편의성 항상
   - DI에 맞게 클래스를 설계하면 의존성을 쉽게 바꿀 수 있는데, 이는 애플리케이션을 테스트할 때 편리하다.
5. 좋은 애플리케이션 설계 도출

DI는 장점이 많지만, 개발자가 의존성 관련 설정이나 코드에 익숙하지 않으면 연결 관계를 파악하기 어렵다는 단점이 있다.

### 의존성 주입을 넘어서

스프링 코어의 DI 기능은 그 자체만으로도 가치 있지만, 스프링의 뛰어난 점은 수많은 추가 기능을 제공하면서도 이 기능들이 DI 원칙에 의해 설계되고 개발됐다는 점이다. 또한, 스프링은 이런 기능을 독자적으로 제공하면서도 다른 도구와 쉽게 연동할 수 있다.

주요 기능

- 자바 9 지원
- 관점 지향 프로그래밍(AOP)
- 스프링 표현식 언어(SpEL)
- 유효성 검증
- 데이터 액세스 (JDBC, Hibernate, JDO, JPA)
- 스프링에서의 객체-XML 매핑
- 트랜잭션 관리
- 웹 MVC
- 웹소켓
- 리모팅 지원
- 메일 지원
- 잡 스케줄링
- 동적 스크립트 언어 지원
- 단순한 예외 처리

## 2장

### 스프링 프레임워크 가져오기

스프링 라이브러리를 가져오는 방법

- 빌드 시스템 사용
- 스프링 깃허브 저장소에서 소스 코드 체크아웃
  라이브러리를 가져올 때는 직접 받기보다 메이븐(Maven)이나 그레이들(Gradle) 같은 빌드툴의 의존성 관리 기능을 사용하는 것이 좋다.

스프링 프레임워크는 Java를 기반으로 만들어졌으므로 스프링을 사용하려면 Java 설치가 필수적이다.

많이 사용되는 Java 관련 약어

- 자바 가상 머신(JVM): 자바 바이트 코드가 실행될 수 있는 실행 환경을 제공하는 사양
- 자바 실행 환경(JRE): JVM의 실제 구현체, JVM 구동 시 필요한 라이브러리 및 파일 포함
- 자바 개발 도구(JDK): JRE, 매뉴얼 및 자바 도구들을 담고 있는 것으로 개발 하려는 컴퓨터에 설치하는 것
  메이븐과 그레이들을 사용하려면 JVM이 필요하다.

> 해당 책에서 다루는 스프링 버전은 5.1.8이며, 소스 코드는 자바8로 작성되었다.

### 스프링 모듈 이해하기

스프링 모듈이란 해당 모듈에 필요한 코드를 모아 놓은 JAR 파일이다. 프로젝트에 필요한 모듈을 적절히 선택해 코드에 사용해야 한다.

메이븐이나 그레이들 같은 도구를 사용하지 않고 애플리케이션에서 사용할 모듈을 직접 선택하는 것은 복잡한 작업이지만, 메이븐의 전이 의존성 지원과 같은 기능을 사용하면 관련 있는 서드파티 라이브러리가 자동으로 포함된다.

메이븐은 자바 애플리케이션에서 사용하는 의존성 관리 도구로 오픈 소스로부터 엔터프라이즈 환경에 이르는 전 분야에서 가장 널리 사용된다. 메이븐은 컴파일부터 테스트와 패키징까지 애플리케이션의 모든 빌드 라이프사이클을 관리하는 강력한 의존성 관리 도구이다.

메이븐 아티팩트는 그룹 ID, 아피택트 ID, 패키지 타입, 버전으로 구분된다. 예를 들어, log4j의 경우 그룹ID는 log4j, 아피택트ID는 log4j, 패키지 타입은 jar이다.

메이븐 구성 파일은 XML로 작성되며 기본 이름은 `pom.xml`이다.

그레이들을 사용할 때도 메이븐과 동일한 규칙을 사용하며 아티팩트를 가져오기 위해 메이븐 중앙 저장소를 사용한다.

차이점은, 그레이들은 설정이 많아질수록 점점 비대해지는 XML 구성 방식 대신 유연한 그루비를 선택했다.

그레이들 구성 파일의 기본 이름은 `build.gradle`이며 메이븐의 `pom.xml`과 같은 역할을 하지만 메이븐 구성 파일보다 읽기 편하다.

### 스프링 문서 사용하기

스프링 프레임워크가 유용한 이유 중 하나는 함께 제공되는 문서가 모든 기능을 포괄하며 문서 품질이 뛰어나다는 점이다. 그 이유는 스프링의 모든 기능이 자바독으로 완전히 문서화될 뿐만 아니라 각 배포한에 포함된 레퍼런스 매뉴얼에도 모든 기능이 담겨 있기 때문이다.

> 스프링이 제공하는 자바독과 레퍼런스 매뉴얼에 익숙해지는 것이 좋다.

## 3장

### 3.1 IoC와 DI

IoC와 DI의 주 목적은 컴포넌트의 의존성을 제공하고, 의존성을 라이프사이클 전반에 걸쳐 관리하는 간편한 메커니즘을 제공하는 것이다.

### 3.2 IoC의 종류

일반적으로 IoC는 의존성 주입과 의존성 룩업으로 나누어진다.

- **의존성 룩업 방식:** 컴포넌트 스스로 의존성의 참조를 가져와야 한다.
  - 의존성 풀(dependency pull)과 문맥에 따른 의존성 룩업(Contextualized Dependency Lookup, CDL)이라는 두 가지 방식으로 나뉜다.
- **의존성 주입 방식:** IoC 컨테이너가 컴포넌트에 의존성을 주입한다.
  - 생성자(constructor) 의존성 주입과 수정자(setter) 의존성 주입의 두 가지 방식으로 나뉜다.

**의존성 풀**

자바 개발자에게 가장 익숙한 IoC 방식으로, 의존성 객체를 직접 요청 또는 검색하여 사용한다.

```java
public class DependencyPull {
    public static void main(String... args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext
           ("spring/app-context.xml");

        MessageRenderer mr = ctx.getBean("renderer", MessageRenderer.class);
        mr.render();
    }
}
```

위 코드에서 `ApplicationContext에서` `getBean` 메서드를 호출하여 `renderer`라는 이름의 빈을 검색하여 가져온다.

**문맥에 따른 의존성 룩업**

CDL은 인터페이스를 구현하는 컴포넌트를 기반으로 동작하고 특정 레지스트리에서 의존성을 가져오는 것이 아니라 자원을 관리하는 컨테이너에서 가져온다.
```java
public interface ManagedComponent {
    void performLookup(Container container);  
}

public interface Container {
    Object getDependency(String key);
}

public class ContextualizedDependencyLookup implements ManagedComponent {
    private Dependency dependency;

    @Override
    public void performLookup(Container container) {
        this.dependency = (Dependency) container.getDependency("myDependency"); 
    }

    @Override
    public String toString() {
    	return dependency.toString();
    }
}
```

`ManagedComponent` 인터페이스의 `performLookup` 메서드는 `Container`로부터 `myDependeny`라는 이름의 의존성을 검색하여 가져온다.

스프링 컨테이너는 크게 BeanFactory와 ApplicationContext, 두 종류의 인터페이스로 구현되어 있다.

스프링 컨테이너의 계층 구조 예시
```plaintext
Spring Container
 ├── BeanFactory
 │    └── DefaultListableBeanFactory
 └── ApplicationContext
      ├── AnnotationConfigApplicationContext
      ├── GenericXmlApplicationContext
      ├── WebApplicationContext
      │    └── ServletWebServerApplicationContext
      └── ConfigurableApplicationContext
```

**생성자 의존성 주입**

컴포넌트의 생성자를 이용해서 해당 컴포넌트가 필요로하는 의존성을 제공하는 방식이다.
```java
public class ConstructorInjection {
    private Dependency dependency;
   
    public ConstructorInjection(Dependency dependency) {
       this.dependency = dependency;
    }

    @Override
    public String toString() {
       return dependeny.toString();
    }
}
```
클래스는 생성자를 통해 의존성을 전달받는다.

**수정자 의존성 주입**

IoC 컨테이너는 자바빈 방식의 수정자 메서드를 이용해 컴포넌트의 의존성을 주입한다. 컴포넌트의 수정자는 IoC 컨테이너가 관리할 수 있도록 의존성을 노출한다. 또한, 수성자 의존성 주입 방식은 가장 널리 사용되기도 하며 구현하기 가장 간단한 의존성 주입 메커니즘 중 하나이다.
```java
public class SetterInjection {
    private Dependency dependency;

    public void setDependency(Dependency dependency) {
       this.dependency = dependency;
    }

    @Override
    public String toString() {
       return dependecy.toString();
    }
}
```
`setter` 메서드를 사용하여 외부에서 의존성을 전달받아 클래스 내부에 저장한다.

**의존성 주입 vs 의존성 룩업**

의존성 주입과 의존성 룩업 중에서 선택해야만 한다면 당연히 의존성 주입이다.

의존성 주입을 이용하면
- 사용자 클래스는 IoC 컨테이너와 완전히 분리돼 자유롭게 사용될 수 있는 반면, 룩업을 이용하면 사용자 클래스는 컨테이너에 의해 정의된 클래스와 인터페이스에 항상 의존하게 된다.
- 테스트 시 실제 구현체 대신 Mock이나 테스트용 의존성을 쉽게 주입할 수 있어 컴포넌트의 테스트가 훨씬 쉬워진다.

또한, 주입 방식을 사용하면 작성해야 하는 코드량이 줄고 코드가 간결해지며, 강력한 IDE를 사용하면 자동화도 가능하다. 즉, 개발이 쉬워진다.

**수정자 주입 vs 생성자 주입**

그렇다면, 수정자 주입과 생성자 주입 중에서는? 사용 사례에 따라 선택해야 한다.

생성자 주입
- 개념:
  - 생성자를 통해 의존성을 전달받아 객체를 생성하는 방식
  - 객체 생성 시 필요한 모든 의존성을 주입받아 초기화
- 사용 상황:
  - 의존성이 필수적이고 변경되지 않아야 하는 상황에서 사용
  - 의존성이 많지 않은 경우
- 장점:
  - 불변성과 일관성이 보장된다
  - 의존성을 명확히 주입할 수 있다
  - 테스트와 유지보수가 쉽다
    - 필수 의존성을 명시적으로 정의하기 때문에 컴파일 시점에 의존성 문제를 발견할 수 있음
    - 불변성 보장 덕분에 안전한 코드 작성 가능
- 단점:
  - 의존성이 많아지면 생성자가 복잡해질 수 있다
    - 그래서 의존성이 많지 않은 경우 사용 권장
  - 의존성을 변경하거나 선택적 의존성을 처리하기가 어렵다

수정자 주입
- 개념:
  - setter 메서드를 통해 객체의 의존성을 주입하는 방식
  - 모든 의존성을 초기화하지 않아도 객체 생성 가능
- 사용 상황:
  - 의존성이 선택적일 때 사용된다
  - 객체 생성 이후 의존성을 동적으로 변경하거나 업데이트할 필요가 있는 경우
- 장점:
  - 객체 생성 시 복잡한 의존성 설정을 지연할 수 있음
- 단점:
  - 의존성이 명시적으로 보장되지 않으므로, 객체의 일관성이 깨질 수 있음
  - 의존성이 주입되지 않은채로 사용될 가능성이 있어 런타임 에러 발생 가능

### 3.3 스프링의 제어 역전

제어 역전은 스프링이 하는 일 중에서 큰 부분을 차지한다. 스프링은 의존 객체에 협력 객체를 자동으로 제공하기 위해 의존성 주입을 이용한다.

하지만, 스프링은 의존성 주입만으로 모든 애플리케이션 컴포넌트를 자동으로 연결할 수 없으며, 이런 경우에 의존성 룩업을 이용해 초기 컴포넌트에 접근해야한다.

웹 애플리케이션을 개발할 때는 스프링의 MVC 기능을 사용해 의존성 룩업 없이도 애플리케이션 전체를 자동으로 연결시킬 수 있다.

### 3.4 스프링의 의존성 주입

**빈과 빈 팩터리**

빈(Bean): 컨테이너가 관리하는 모든 컴포넌트로, 객체 관리와 의존성 주입을 책임진다

빈 팩터리(BeanFactory): 컴포넌트(빈)의 라이프사이클, 의존성을 관리하는 인터페이스

쉽게 말해, 빈 팩터리는 도구 상자이고 빈은 애플리케이션이 사용할 도구이다. 추가로, ApplicationContext는 빈 팩터리를 기반으로 한 확장판 개념이다.

애플리케이션에서 DI 기능이 필요하다면 BeanFactory 인터페이스를 이용해 스프링 DI 컨테이너와 직접 연동할 수 있다. 이때는 애플리케이션에서 BeanFactory 인터페이스를 구현한 클래스의 인스턴스를 생성하고 빈과 의존성 정보를 구성해야 한다. 

BeanFactory 구성을 프로그래밍으로 할 수 있지만, 구성 파일을 사용해 외부에서 구성하는 것이 좀 더 일반적이다. BeanDefinitionReader 인터페이스를 구현한 BeanFactory 클래스에서 BeanDefinition 데이터를 읽을 수 있다.

따라서 사용자는 BeanFactory 내에서 각 빈에 ID나 이름을 지정하여 사용자 빈을 식별하고 빈 ID나 이름을 사용해 빈을 가져와서 의존성을 확립할 수 있다.

**BeanFactory 구현체**

```java
public interface Oracle {
    String defineMeaningOfLife();
}
```
```java
public class BookwormOracle implements Oracle {
    private Encyclopedia encyclopedia;

    public void setEncyclopedia(Encyclopedia encyclopedia) {
       this.encyclopedia = encyclopedia;
    }

    @Override
    public String defineMeaningOfLife() {
       return "Encyclopedias are a waste of money - go see the world instead";
    }
}
```
```java
public class XmlConfigWithBeanFactory {

	public static void main(String... args) {
		DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
		XmlBeanDefinitionReader rdr = new XmlBeanDefinitionReader(factory);
		rdr.loadBeanDefinitions(new
				ClassPathResource("spring/xml-bean-factory-config.xml"));
		Oracle oracle = (Oracle) factory.getBean("oracle");
		System.out.println(oracle.defineMeaningOfLife());
	}
}
```
위 예제의 `DefaultListableBeanFactory`는 BeanFactory를 구현한 클래스로, 실제로 빈을 관리하는 역할을 한다. `XmlBeanDefinitionReader`를 사용해 XML 설정 파일에서 빈 정의를 읽어와 BeanFactory에 등록한다. 이후, `getBean("oracle")`을 통해 XML에 정의된 oracle 빈을 가져온다.

**애플리케이션 컨텍스트(ApplicationContext)**

BeanFactory를 상속한 인터페이스로, DI 서비스 외에도 트랜잭션, AOP, 애플리케이션 이벤트 처리와 같은 여러 서비스를 제공한다.

### 3.5 애플리케이션 컨텍스트 구성하기

**스프링 구성 옵션 설정하기**

기존에 스프링은 프로퍼티나 XML 파일을 사용해 빈을 정의했지만, JDK 5 이후에 스프링이 애너테이션을 지원하면서 애플리케이션 컨텍스트를 구성하는 데 자바 애너테이션을 지원하기 시작했다.
- XML 파일 사용 시 구성을 자바 코드와 분리해 외부에서 관리할 수 있다.
- 애너테이션 사용 시 개발자가 코드 내에서 DI 구성을 정의하고 확인할 수 있다.
두 가지 방법 다 장단점이 있으며 어느 것이 더 나은지에 대해서는 정답이 없다. (두 가지 방식을 혼용할 수도 있음)

**기본 구성의 개요**

애플리케이션에서 스프링의 애너테이션을 사용하려면 `<context:component-scan>` 태그를 XML 구성에 선언해야 한다.
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context 
          http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan 
          base-package="com.apress.prospring5.ch3.annotated"/>
</beans>
```

**스프링 컴포넌트 선언하기**

스프링은 애플리케이션 컨텍스트를 부트스트랩할 때 컴포넌트를 찾아서 빈 인스턴스를 생성한다.
```java
@Component("Provider")
public class HelloWorldMessageProvider implements MessageProvider {

    @Override
    public String getMessage() {
        return "Hello World!";
    }
}
```
`@Component`를 통해 해당 클래스가 스프링이 관리하는 빈임을 선언한다.
```java
@Service("renderer")
public class StandardOutMessageRenderer implements MessageRenderer {
    private MessageProvider messageProvider;

    @Override
    public void render() {
        if (messageProvider == null) {
            throw new RuntimeException(
            "messageProvider 클래스의 프로퍼티를 설정해야 합니다:"
            + StandardOutMessageRenderer.class.getName());
        }

        System.out.println(messageProvider.getMessage());
    }

    @Override
    @Autowired
    public void setMessageProvider(MessageProvider provider) {
        this.messageProvider = provider;
    }

    @Override
    public MessageProvider getMessageProvider() {
        return this.messageProvider;
    }
}
```
`@Autowired`를 통해 스프링 컨테이너가 필요한 빈을 찾아서 자동으로 주입한다.

**자바 구성 사용하기**

`@Configuration` 애너테이션을 적용한 구성 클래스 내에는 IoC 컨테이너가 빈 인스턴스를 만들 때 직접 호출하는 `@Bean` 애너테이션이 적용된 메서드가 포함되어 있다.
```java
@Configuration
public class HelloWorldConfiguration {

    @Bean
    public MessageProvider provider() {
        return new HelloWorldMessageProvider();
    }

    @Bean
    public MessageRenderer renderer() {
        MessageRenderer renderer = new StandardOutMessageRenderer();
        renderer.setMessageProvider(provider());

        return renderer;
    }
}
```
`renderer` 메서드는 `provider`를 호출하여 빈을 가져오고 `StandardOutMessageRenderer`에 의존성을 주입한다.

**주입 인자 사용하기**

스프링은 다른 컴포넌트나 단순 값 외에 컬렉션, 프로퍼티, 다른 팩터리의 빈을 주입할 수 있도록 주입 인자에 많은 옵션을 지원한다.

**단순 값 주입**

주입할 값을 `<value>` 태그로 감싸 구성 태그에 지정하면 된다. 애너테이션을 사용하려면 `@Value` 애너테이션을 빈 프로퍼티에 적용한다.
```java
@Service("injectSimple")
public class InjectSimple {

    @Value("John Mayer")
    private String name;
    @Value("39")
    private int age;
    @Value("1.92")
    private float height;
    @Value("false")
    private boolean programmer;
    @Value("1241401112")
    private Long ageInSeconds;
    ...
}
```

**SpEL을 사용해 값 주입**

표현식을 동적으로 평가하여 그 결과를 스프링의 애플리케이션 컨텍스트 내에서 사용할 수 있다.
```java
@Service("injectSimpleSpel")
public class InjectSimpleSpel {
    
    @Value("#{injectSimpleConfig.name}")
    private String name;

    @Value("#{injectSimpleConfig.age + 1}")
    private int age;

    @Value("#{injectSimpleConfig.height}")
    private float height;

    @Value("#{injectSimpleConfig.programmer}")
    private boolean programmer;

    @Value("#{injectSimpleConfig.ageInSeconds}")
    private Long ageInSeconds;
    ...
}
```
```java
@Component("injectSimpleConfig")
public class InjectSimpleConfig {
    private String name = "John Mayer";
    private int age = 40;
    private float height = 1.92f;
    private boolean programmer = false;
    private Long ageInSeconds = 1_241_401_112L;
    ...
}
```

**Collection 주입**

개별 빈이나 값이 아닌 객체의 컬렉션에 접근해야 하는 일이 종종 있다.
```java
@Service("injectCollection")
public class CollectionInjection {

    @Resource(name="map")
    private Map<String, Object> map;

    @Resource(name="props")
    private Properties props;

    @Resource(name="set")
    private Set set;

    @Resource(name="list")
    private List list;
    ...
}
```
`@Autowired` 대신 `@Resource`를 사용한 이유는 `@Autowired` 애너테이션이 배열, 컬렉션, 맵을 해당 컬렉션의 값 타입에서 파생된 대상 빈 타입을 가져와 처리하기 때문이다. 예를 들어 `List <ContentHolder>` 타입의 애트리뷰트가 있다고 가정하면, 스프링은 구성 파일에 선언된 `<util:list>` 대신 `ContentHolder` 타입의 모든 빈을 주입하려고 시도하고, 그 결과 의도치 않은 의존성이 주입되거나 `ContextHolder` 타입 빈의 정의되지 않아 예외가 발생할 수 있다.

또한, `@Autowired`와 `@Qualifier`, 두 애너테이션을 조합해 사용해도 동일한 목적은 달성하지만 하나의 애너테이션만을 사용하는 것이 바람직하다.

**빈 명명 규칙 이해하기**

모든 빈은 애플리케이션 컨텍스트 내에서 고유한 하나 이상의 이름을 가져야 한다.

스프링이 빈 이름을 결정하는 과정
1. `<bean>` 태그에 id 애트리뷰트의 값을 지정하면 해당 값이 이름으로 사용된다.
2. id 애트리뷰트에 값이 없으면 name 애트리뷰트를 찾아 그중 첫 번째 이름을 사용한다.
3. id와 name 둘 다 값이 없으면 빈의 클래스 이름을 빈 이름으로 사용한다.
다음은 세 가지 빈 명명 규칙을 설정한 구성 파일의 예시이다.
```xml
<beans ...>
    <bean id="string1" class="java.lang.String"/>
    <bean name="string2" class="java.lang.String"/>
    <bean class="java.lang.String"/>
</beans>
```
> 클래스 이름을 자동으로 사용하는 방식은 피하는 것이 좋다.


애너테이션을 이용한 빈 명명 규칙
```java
@Component("johnMayer")
public class Singer {
    ...
}
```
`@Bean`의 name 애트리뷰트를 사용해 별칭을 설정할 수 있다.
```java
@Configuration
public static class AliasBeanConfig {
    @Bean(name={"johnMayer", "john", "jonathan", "johnny"})
    public Singer singer() {
        return new Singer();
    }
}
```
문자열 배열의 첫 번째 값이 id가 되고 나머지 값들은 별칭이 된다.

**빈 생성 방식 이해하기**

기본적으로 스프링의 모든 빈은 싱글턴이다. 즉, 스프링은 빈의 단일 인스턴스를 유지하고 관리하며 `ApplicationContext.getBean()`에 대한 모든 호출은 동일한 인스턴스를 반환한다.

싱글턴: 스프링 컨테이너가 관리하는 빈의 스코프 중 하나
싱글턴 패턴: 애플리케이션에서 클래스의 인스턴스를 하나만 생성하도록 보장하는 패턴

인스턴스 생성 방식을 싱글턴에서 비싱글턴으로 변경하는 방법
```java
@Component("nonSingleton")
@Scope("prototype")
public class Singer {
    private String name = "unknown";

    public Singer(@Value("John Mayer") String name) {
        this.name = name;
    }
}
```
`@Scope` 애트리뷰트를 추가하고 값을 `prototype`으로 설정하면 스프링은 애플리케이션이 빈 인스턴스를 요청할 때마다 새로운 인스턴스를 생성한다.

보통은 싱글턴 사용이 기본 방식이지만 상황에 따라 고려해야 한다.

싱글턴 사용을 고려하는 경우:
- 상태가 없는 공유 객체
- 읽기 전용 상태를 갖는 공유 객체
- 공유 상태를 갖는 공유 객체
- 쓰기 가능 상태를 갖는 대용량 처리 객체

비싱글턴 사용을 고려하는 경우:
- 쓰기 가능한 상태를 갖는 객체
- private 상태를 갖는 오브젝트


### 3.6 빈에 자동와이어링하기

스프링이 제공하는 다섯 가지 자동와이어링 방식
- byName: 애플리케이션 컨텍스트에서 이름이 같은 빈을 찾아서 연결 시도
- byType: 애플리케이션 컨텍스트에서 동일한 타입의 빈을 대상 빈의 각 프로퍼티에 연결 시도
- Constructor: 주입이 수정자가 아닌 생성자를 이용해 이루어진다는 점을 제외하면 타입에 의한 와이어링과 일치
- default: 빈에 기본 생성자가 있다면 byType 방식을 이용, 그렇지 않으면 Constructor 방식을 이용
- no: 기본값

애너테이션을 이용한 자동와이어링 방식은 byType이다.

이름을 기반으로 자동와이어링 하려는 경우 `@Autowired` 애너테이션과 함께, 주입되어야 하는 빈의 이름을 인자로 전달하는 `@Qualifier` 애너테이션을 적용해야 한다.

예제를 통해 알아보자.
```java
@Component
@Lazy
public class TrickyTarget {

    Foo fooOne;
    Foo fooTwo;
    Bar bar;

    public TrickyTarget() {
	 	System.out.println("Target.constructor()");
	}

	public TrickyTarget(Foo fooOne) {
		System.out.println("Target(Foo) 호출");
	}

	public TrickyTarget(Foo fooOne, Bar bar) {
		System.out.println("Target(Foo, Bar) 호출");
	}

	@Autowired
	public void setFooOne(Foo fooOne) {
		this.fooOne = fooOne;
		System.out.println("프로퍼티 fooOne 설정");
	}

	@Autowired
	public void setFooTwo(Foo foo) {
		this.fooTwo = foo;
		System.out.println("프로퍼티 fooTwo 설정");
	}

	@Autowired
	public void setBar(Bar bar) {
		this.bar = bar;
		System.out.println("프로퍼티 bar 설정");
	}

	public static void main(String... args) {
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
		ctx.load("classpath:spring/app-context-05.xml");
		ctx.refresh();

		TrickyTarget t = ctx.getBean(TrickyTarget.class);

		ctx.close();
	}
}
```
`Foo` 클래스와 `Bar` 클래스가 다음과 같다면
```java
@Component
public class Foo {
}
```
```java
@Component
public class Bar {
}
```
`TrickyTarget` 클래스가 실행될 때 다음과 같다.
```plaintext
Target.constructor()
프로퍼티 fooOne 설정
프로퍼티 fooTwo 설정
프로퍼티 bar 설정
```

기본 자동와이어링 방식이 byType이기 때문에 `TrickyTarget` 클래스에 빈 이름을 지정하거나 `Bar` 타입 빈에 이름을 제공하더라도 동일한 결과가 출력된다.

하지만 빈 타입들이 서로 연관되면 상황이 복잡해진다.
```java
public interface Foo{
}
```
```java
@Component
public class FooImplOne implements Foo {
}
```
```java
@Component
public class FooImplOne implements Foo {
}
```

만약 이 상태에서 TrickyTarget 클래스를 변경하지 않고 그대로 실행하면, `UnsatisfiedDependencyException`이 발생한다. 이는 스프링이 `setFoo` 메서드를 사용해 주입해야 하는 빈이 어떤 빈인지 알지 못한다는 뜻이다.

이를 해결하기 위해서는 두 가지 방법이 있다. `@Primary`, `@Qualifier`

`@Primary` 애너테이션을 사용한 클래스는 타입을 기반으로 자동와이어링을 할 때 스프링에게 자신의 빈의 우선순위를 높게 지정한다.
```java
@Component
@Primary
public class FooImplOne implements Foo {
}
```
다시 `TrickyTarget` 클래스를 실행하면 예상하는 결과가 출력된다.
```plaintext
Target.constructor()
프로퍼티 fooOne 설정
프로퍼티 fooTwo 설정
프로퍼티 bar 설정
```

`@Qualifier` 애너테이션은 모호한 수정자인 `setFooOne()`와 `setFooTwo()`에 `@Autowired`와 함께 적용된다.
```java
@Component
@Lazy
public class TrickyTarget {

	...
	@Autowired
	@Qualifier("fooImplOne")
	public void setFooOne(Foo fooOne) {
		this.fooOne = fooOne;
		System.out.println("프로퍼티 fooOne 설정");
	}

	@Autowired
	@Qualifier("fooImplTwo")
	public void setFooTwo(Foo foo) {
		this.fooTwo = foo;
		System.out.println("프로퍼티 fooTwo 설정");
	}
	...
}
```
이번 역시 `TrickyTarget` 클래스를 실행하면 동일한 결과가 출력된다.
```plaintext
Target.constructor()
프로퍼티 fooOne 설정
프로퍼티 fooTwo 설정
프로퍼티 bar 설정
```

**자동와이어링을 사용하는 경우**

자동와이어링은 대부분의 경우 사용하지 않는 것이 좋다. 소규모 애플리케이션에서 시간을 절약할 수 있지만 대규모 애플리케이션에서는 유연성이 떨어진다. 따라서 중요한 애플리케이션이라면 자동와이이링은 사용하지 말아야 한다.

### 3.7 빈 상속 설정하기

때로는 같은 타입의 빈이나 공유 인터페이스를 구현하는 빈을 여러 개 정의할 필요가 있다.

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="parent" class="com.apress.prospring5.ch3.xml.Singer"
        p:name="John Mayer" p:age="39" />
    
    <bean id="child" class="com.apress.prospring5.ch3.xml.Singer"
        parent="parent" p:age="0"/>
</beans>
```

위 코드에서 `child` 빈을 위한 `<bean>` 태그에서 `parent`라는 별도의 애트리뷰트를 작성하여 `parent` 빈을 `child` 빈의 부모로 간주하도록 한다.

`child` 빈은 `age` 프로퍼티의 값을 설정했기 때문에 스프링은 이 값을 빈에 절달하지만, `name` 프로퍼티는 값을 갖지 않으므로 `parent` 빈에 주어진 값을 사용한다.
```java
public class Singer {
    private String name;
    private int age;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
        
    public String toString() {
        return "\t이름: " + name + "\n\t" + "나이: " + age;
    }
}
```
```java
public class InheritanceDemo {

	public static void main(String... args) {
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
		ctx.load("classpath:spring/app-context-xml.xml");
		ctx.refresh();

		Singer parent = (Singer) ctx.getBean("parent");
		Singer child = (Singer) ctx.getBean("child");

		System.out.println("부모:\n" + parent);
		System.out.println("자식:\n" + child);
	}
}
```

출력 결과는 다음과 같다.
```plaintext
부모:
   이름: John Mayer
   나이: 39
자식:
   이름: John Mayer
   나이: 0
```

보이는 대로 `child` 빈은 `name` 프로퍼티를 갖지 않으므로 `parent의` 빈으로부터 상속 받지만, `age` 프로퍼티는 자신의 값을 제공받는다.
