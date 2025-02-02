# 4장 스프링 구성 상세와 스프링 부트

스프링의 기본 IoC 기능을 보완하고 확장하는 다양한 서비스에 대한 챕터

- 빈 라이프사이클 관리
- 빈이 스프링을 알게 하기
- 팩토리 빈 사용하기
- 자바빈 PropertyEditor 사용하기
- 스프링 ApplicationContext 자세히 살펴보기
- 자바 클래스로 스프링 구성하기
- 스프링 부트 사용하기
- 개선된 구성 방법 사용하기
- 구성에 그루비(Groovy)를 사용하기

## 4.1 스프링이 애플리케이션 이식성에 미치는 영향

4장에서 소개되는 기능들은 스프링에 국한된 기능이므로 다른 IoC는 대부분 이런 기능을 지원하지 않음

하지만 실제로는 더 넓은 의미에서 (특정 벤더에 종속되지 않으면서 무료로 제공되는 오픈 소스 프레임워크이기 때문에)애플리케이션의 이식성은 더욱 커질 것

## 4.2 빈 라이프사이클 관리

빈이 라이프사이클 이벤트 통지를 받을 시 이벤트 발생 시점에 관련 처리 가능

- 초기화 이후 → 개발자가 구성한 대로 빈에 모든 프로퍼티 값을 설정하고 의존성 점검을 마치면 발생
- 소멸 이전 → 빈 인스턴스를 소멸시키기 바로 전에 발생

스프링에서 빈에 대한 라이프사이클 이벤트를 받을 수 있는 3가지 메커니즘을 제공

1. 인터페이스 기반
2. 메서드 기반
3. 애너테이션 기반

라이프사이클 통지를 받을 때 애플리케이션 요구사항이 중요

이식성과 빈 한 두개 만을 정의 → **메서드 기반 메커니즘**

이식성에 크게 구애받지 않거나 라이프사이클 통지가 필요한 동일 타입 빈을 다수 정의 → **인터페이스 기반 메커니즘**

애너테이션 기반 구성을 사용하면서 사용 중인 IoC 컨테이너가 JSR-250을 지원 → **애너테이션 메커니즘**
![image](https://github.com/user-attachments/assets/ff14749c-3772-4b29-8f10-65950f5649ea)

## 4.3 빈 생성 시점에 통지 받기

빈 초기화 시점을 알 시 **`모든 의존성이 제대로 주입됐는지 확인 가능`**

초기화 콜백을 통해 의존성 검사 뿐만이 아니라 모든 작업을 콜백 내부 가능

### 4.3.1 빈 생성 시 메서드 실행하기

초기화 콜백을 받는 방법 중 하나는 빈의 메서드 하나를 지정해 초기화 콜백으로 사용함을 스프링에 설정하는 것, 이러한 방법은 빈이 몇개 안되거나 애플리케이션이 스프링과 결합되지 않게 할 때 유용

초기화 메서드가 가진 단점은 **`인자를 받을 수 없다는 것`** 

```java
public class Singer {
    private static final String DEFAULT_NAME = "Eric Clapton";

    private String name;
    private int age = Integer.MIN_VALUE;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    private void init() {
        System.out.println("빈 초기화");

        if (name == null) {
            System.out.println("기본 이름 사용");
            name = DEFAULT_NAME;
        }

        if (age == Integer.MIN_VALUE) {
            throw new IllegalArgumentException(
                    Singer.class +" 빈 타입에는 반드시 age 프로퍼티를 설정해야 합니다.");
        }
    }

    public String toString() {
        return "\t이름: " + name + "\n\t나이: " + age;
    }

    public static void main(String... args) {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-xml.xml");
        ctx.refresh();

        getBean("singerOne", ctx);
        getBean("singerTwo", ctx);
        getBean("singerThree", ctx);

        ctx.close();
    }

    public static Singer getBean(String beanName, ApplicationContext ctx) {
        try {
            Singer bean = (Singer) ctx.getBean(beanName);
            System.out.println(bean);
            return bean;
        } catch (BeanCreationException ex) {
            System.out.println("빈 구성 도중 에러 발생: "
                    + ex.getMessage());
            return null;
        }
    }
}

```

해당 코드에서 singerThree에서 `BeanCreationException`  발생

### 4.3.2 InitializingBean 인터페이스 구현하기

```java
 public class SingerWithInterface implements InitializingBean {
		
		,,,
		
    public void afterPropertiesSet() throws Exception {
        System.out.println("빈 초기화");

        if (name == null) {
            System.out.println("기본 가수 이름 설정");
            name = DEFAULT_NAME;
        }

        if (age == Integer.MIN_VALUE) {
            throw new IllegalArgumentException(
                    SingerWithInterface.class 
                    +" 빈 타입에는 반드시 age 프로퍼티를 설정해야 합니다.");
        }
    }

```

크게 바뀐 것 없이 클래스 이름이 바뀐 것, 빈이 InitializingBean을 구현했다는 것, 구현 로직이 afterPropertiesSet()메서드로 이동했다는 것 정도이며 구성 파일에서는 init-method 속성이 빠진 정도

이로 인해 `SimpleBeanWithInterface`가 `InitializingBean` 인터페이스를 구현했으므로 스프링이 초기화 콜백으로 어느 메서드를 호출해야할지 인지하기 때문에 초기화 메서드를 지정하는 추가 구성이 필요 없어짐

### 4.3.3 JSR-250 @PostConstruct 애너테이션 사용하기

```java
    @PostConstruct
    private void init() throws Exception {
        System.out.println("빈 초기화");

       if (name == null) {
            System.out.println("기본 가수 이름 설정");
            name = DEFAULT_NAME;
        }

        if (age == Integer.MIN_VALUE) {
            throw new IllegalArgumentException(
                SingerWithJSR250.class 
                +" 빈 타입에는 반드시 age 프로퍼티를 설정해야 합니다.");
        }
    }

```

`init()`메서드 위에 `@PostConstruct` 애너테이션을 적용한 것말고는 차이는 없음

초기화 메서드 이름은 아무거나 사용할 수 있으며 애너테이션을 적용했으므로 구성 파일에 context 네임스페이스가 제공하는 `<context:annotation-config>`추가 필요

**정리**

초기화 메서드를 사용할 시 스필ㅇ과 결합 되지 않는 효과가 있지만 초기화가 필요한 모든 빈을 빠짐 없이 구성에 등록해야함

`InitializingBean` 인터페이스 사용 시 빈클래스의 모든 인스턴스 초기화 콜백을 한번에 지정 가능하지만 애플리케이션을 스프링에 결합해야 함

애너테이션을 사용할 시 메서드에 애너테이션을 적용해야하며 사용하는 IoC컨테이너가 JSR-250을 확실히 지원해야 함

### 4.3.4 @Bean으로 초기화 메서드 선언하기

`@Bean` 애너테이션에 `initMethod` 속성을 추가하고 속성 값에 초기화 메서드 이름을 지정 → 앞선 예제들과 동일한 결과 출력

```java
public class SingerConfigDemo {

    @Configuration
    static class SingerConfig{

        @Lazy
        @Bean(initMethod = "init")
        Singer singerOne() {
            Singer singerOne =     new Singer();
            singerOne.setName("John Mayer");
            singerOne.setAge(39);
            return singerOne;
        }

        @Lazy
        @Bean(initMethod = "init")
        Singer singerTwo() {
            Singer singerTwo =     new Singer();
            singerTwo.setAge(72);
            return singerTwo;
        }

        @Lazy
        @Bean(initMethod = "init")
        Singer singerThree() {
            Singer singerThree =     new Singer();
            singerThree.setName("John Butler");
            return singerThree;
        }
    }

    public static void main(String... args) {
        GenericApplicationContext ctx = new AnnotationConfigApplicationContext(SingerConfig.class);

        getBean("singerOne", ctx);
        getBean("singerTwo", ctx);
        getBean("singerThree", ctx);

        ctx.close();
    }

}

```

### 4.3.5 초기화 메서드 해석 순서 이해하기

@PostConstruct 애너테이션 적용 메서드 → afterPropertiesSet() → 구성파일에 기재한 초기화 메서드

그 이유는 아래와 같은 빈 생성 단계 때문
![image](https://github.com/user-attachments/assets/af077965-3577-4976-856d-d7c46d08c8c0)

## 4.4 빈 소멸 시점에 통지 받기

초기화 콜백과 쌍으로 사용되며 메커니즘이 매우 유사함

아래 모든 예시는 동일 결과를 로그로 출력함

![image](https://github.com/user-attachments/assets/745cf27f-4f27-42ae-abee-500ba1561e85)


### 4.4.1 빈이 소멸될 때 메서드 실행하기

```java
public class DestructiveBean {
    private File file;
    private String filePath;
    
    public void afterPropertiesSet() throws Exception {
        System.out.println("빈을 초기화합니다.");

        if (filePath == null) {
            throw new IllegalArgumentException(
                DestructiveBean.class + "에 filePath 프로퍼티를 지정해야 합니다.");
        }

        this.file = new File(filePath);
        this.file.createNewFile();

        System.out.println("파일 존재여부: " + file.exists());
    }

    public void destroy() {
        System.out.println("빈을 소멸합니다.");

        if(!file.delete()) {
            System.err.println("에러: 파일 삭제에 실패했습니다.");
        }

        System.out.println("파일 존재여부: " + file.exists());
    }

    public void setFilePath(String filePath) {
        this.filePath = filePath;
    }

    public static void main(String... args) throws Exception {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-xml.xml");
        ctx.refresh(); 

        DestructiveBean bean = (DestructiveBean) ctx.getBean("destructiveBean");

        System.out.println("destroy() 호출 시작");
        ctx.destroy();
        System.out.println("destroy() 호출 종료");
    }
}

```

초기화 시에 생성한 파일을 삭제하는 destroy() 메서드를 정의

GenericXmlApplicationContext에서 DestructiveBean 타입 빈을 가져온 뒤 GenericXmlApplicationContext의 destroy() 메서드를 호출해 스프링이 관리 중인 모든 싱글턴 빈을 소멸하게 함

```java
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="destructiveBean" 
        class="com.apress.prospring5.ch4.DestructiveBean"
        destroy-method="destroy"
        init-method="afterPropertiesSet"
        p:filePath=
        "#{systemProperties['java.io.tmpdir']}#{systemProperties['file.separator']}test.txt"/>
</beans>

```

destroy-method 속성에 소멸 콜백을 destroy() 메서드로 지정

### 4.4.2 DisposableBean 인터페이스 구현하기

초기화 콜백과 같이 스프링은 DisposableBean 인터페이스를 제공하면 빈이 이 인터페이스를 구현 시 소멸 콜백 호출을 받을 수 있음

```java
public class DestructiveBeanWithInterface implements InitializingBean, DisposableBean {
    private File file;
    private String filePath;
    
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("빈을 초기화합니다.");

        if (filePath == null) {
            throw new IllegalArgumentException(
                DestructiveBeanWithInterface.class
                + "에 filePath 프로퍼티를 지정해야 합니다.");
        }

        this.file = new File(filePath);
        this.file.createNewFile();

        System.out.println("파일 존재 여부: " + file.exists());
    }

    @Override
    public void destroy() {
        System.out.println("빈을 소멸합니다.");

        if(!file.delete()) {
            System.err.println("에러: 파일 삭제에 실패했습니다.");
        }

        System.out.println("파일 존재 여부: " + file.exists());
    }

    public void setFilePath(String filePath) {
        this.filePath = filePath;
    }

    public static void main(String... args) throws Exception {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-xml.xml");
        ctx.refresh(); 

        DestructiveBeanWithInterface bean = 
            (DestructiveBeanWithInterface) ctx.getBean("destructiveBean");

        System.out.println("destroy() 호출 시작");
        ctx.destroy();
        System.out.println("destroy() 호출 종료");
    }
}

```

콜백 메서드를 사용하는 코드와 콜백 인터페이스를 사용하는 코드 사이에서는 큰 차이가 존재하지 않으며, 다른 점은 destroy-method와 init-method 속성이 사라졌다는 점

```java
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="destructiveBean"
        class="com.apress.prospring5.ch4.DestructiveBeanWithInterface"
        p:filePath=
        "#{systemProperties['java.io.tmpdir']}#{systemProperties['file.separator']}test.txt"/>
</beans>

```

### 4.4.3 JSR-250 @PreDestroy 애너테이션 사용하기

@PreDestroy는 @PostConstruct 애너테이션과 반대라 생각하면 됨

```java
public class DestructiveBeanWithJSR250 {
    private File file;
    private String filePath;
    
    @PostConstruct
    public void afterPropertiesSet() throws Exception {
        System.out.println("빈을 초기화합니다.");

        if (filePath == null) {
            throw new IllegalArgumentException(
                DestructiveBeanWithJSR250.class + "에 filePath 프로퍼티를 지정해야 합니다.");
        }

        this.file = new File(filePath);
        this.file.createNewFile();

        System.out.println("파일 존재 여부: " + file.exists());
    }

    @PreDestroy
    public void destroy() {
        System.out.println("빈을 소멸합니다.");

        if(!file.delete()) {
            System.err.println("에러: 파일 삭제에 실패했습니다.");
        }

        System.out.println("파일 존재 여부: " + file.exists());
    }

    public void setFilePath(String filePath) {
        this.filePath = filePath;
    }

    public static void main(String... args) throws Exception {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-annotation.xml");
        ctx.refresh(); 

        DestructiveBeanWithJSR250 bean = 
            (DestructiveBeanWithJSR250) ctx.getBean("destructiveBean");

        System.out.println("destroy() 호출 시작");
        ctx.destroy();
        System.out.println("destroy() 호출 종료");
    }
}

```

<context:annotation-config> 태그를 사용함

```java
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
    
    <bean id="destructiveBean"
        class="com.apress.prospring5.ch4.DestructiveBeanWithJSR250"
        p:filePath=
        "#{systemProperties['java.io.tmpdir']}#{systemProperties['file.separator']}test.txt"/>
</beans>

```

### 4.4.4 @Bean을 사용해 소멸 메서드 정의하기

@Bean 애너테이션에 destroyMethod 속성을 추가하고 속성 값에 메서드 이름을 지정하는 방식

```java
public class DestructiveBeanConfigDemo {

    @Configuration
    static class DestructiveBeanConfig {

        @Lazy
        @Bean(initMethod = "afterPropertiesSet", destroyMethod = "destroy")
        DestructiveBean destructiveBean() {
            DestructiveBean destructiveBean = 
                    new DestructiveBean();
            destructiveBean.setFilePath(System.getProperty("java.io.tmpdir") +
                    System.getProperty("file.separator") + "test.txt");
            return destructiveBean;
        }

    }

    public static void main(String... args) {
        GenericApplicationContext ctx =
                new AnnotationConfigApplicationContext(DestructiveBeanConfig.class);

        ctx.getBean(DestructiveBean.class);
        System.out.println("destroy() 호출 시작");
        ctx.destroy();
        System.out.println("destroy() 호출 종료");
    }
}

```

### 4.4.5 해석 순서 이해하기

@PreDestroy →DisposableBean.destroy()→xml 소멸 메서드

### 4.4.6 셧다운 후크 사용하기

소멸 콜백은 자동으로 호출되지 않으며 `AbstractApplicationContext.destroy()`를 호출해야함

종료지점이 여러 곳이면 `destroy()` 호출이 복잡해지므로 셧다운 후크를 통해 `ApplicationContext`의 모든 구상 클래스가 상속한 `AbstractApplicationContext`의 `destory()` 메서드를 호출 함

대표적인 방법은 `registerShutdownHook()` 메서드를 사용하는 것

```java
public class DestructiveBeanWithHook {
		...

    public static void main(String... args) throws Exception {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-annotation.xml");
        ctx.**registerShutdownHook**();
        ctx.refresh();

        ctx.getBean(DestructiveBeanWithHook.class);
    }
}

```

## 4.5 빈이 스프링을 알게하기

빈이 커테이너에서 어떤 이름으로 사용되는지 알아내 부가 처리 작업 등을 수행할 때 사용하는 기능

### 4.5.1 BeanNameAware 인터페이스 사용하기

BeanNameAware 인터페이스는 setBeanName 메서드를 가지고 있으며 라이프사이클 콜백을 호출 전에 setBeanName() 메서드를 호출

아래는 BeanNameAware 를 사용해 빈의 이름을 얻어온 후 나중에 얻어온 빈 이름을 콘솔에 출력하는 코드

```java
public class NamedSinger implements BeanNameAware {
    private String name;

    /** @Implements {@link BeanNameAware#setBeanName(String)} */
    public void **setBeanName**(String beanName) {
        **this.name = beanName**;
    }

    public void sing() {
        System.out.println("Singer [" + name + "] - sing()");
    }
}
```

```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="johnMayer" class="com.apress.prospring5.ch4.NamedSinger"/>
</beans>
```

ApplicationContext에서 Singer 인스턴스를 가져와 sing() 메서드 호출하기

```java
public class NamedSingerDemo {
    public static void main(String... args) {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-xml.xml");
        ctx.refresh();

        NamedSinger bean = (NamedSinger) ctx.getBean("johnMayer");
        bean.sing();

        ctx.close();
    }
}

```

### 4.5.2 ApplicationContextAware 인터페이스 사용하기

ApplicationContextAware 인터페이스를 통해 ApplicationContext에서 빈이 구성될 때 셧다운 후크를 자동으로 생성하고 구성할 수 있음

```java
public class ShutdownHookBean implements ApplicationContextAware {
    private ApplicationContext ctx;

    /** @Implements {@link ApplicationContextAware#setApplicationContext(ApplicationContext)}  }*/
    public void setApplicationContext(ApplicationContext ctx)
        throws BeansException {

        if (ctx instanceof GenericApplicationContext) {
            ((GenericApplicationContext) ctx).registerShutdownHook();
        }
    }
}

```

설정 파일에서는 빈 등록 외에 특별한 구성이 필요 없다

```java
    <bean id="shutdownHook" 
        class="com.apress.prospring5.ch4.ShutdownHookBean"/>

```

## 4.6 FactoryBean 사용하기

new 연산자로 간단히 생성할 수 없는 의존성을 주입하기 위해 스프링 구문으로는 생성 및 관리 불가능한 객체 관리 어댑터인 FactoryBean 인터페이스를 제공

FeactoryBean은 다른 빈을 생성하는 팩터리 역할을 담당

### 4.6.1 FactoryBean 예제 : MessageDigestFactoryBean

FactoryBean을 이용해 algorithmName이라는 프로퍼티를 주입하고 초기화 콜백에 주입된 프로퍼티를 통해 MessageDigest.getInstance()를 호출하는 메서드를 캡슐화

```java
public class **MessageDigestFactoryBean** implements 
      FactoryBean<MessageDigest>, InitializingBean {
    private String algorithmName = "MD5";

    private MessageDigest messageDigest = null;

    public MessageDigest getObject() throws Exception {
       return messageDigest;
    }

    public Class<MessageDigest> getObjectType() {
        return MessageDigest.class;
    }

    public boolean isSingleton() {
        return true;
    }

    public void afterPropertiesSet() throws Exception {
        messageDigest = **MessageDigest.getInstance**(algorithmName);
    }

    public void setAlgorithmName(String algorithmName) {
        this.algorithmName = algorithmName;
    }
}
```

두개의 인스턴스를 내부에 갖고 있을 시 digest() 메서드로 전달받은 메시지의 해시 값을 출력하는 코드 

```java
public class MessageDigester {
    private MessageDigest digest1;
    private MessageDigest digest2;

    public void setDigest1(MessageDigest digest1) {
        this.digest1 = digest1;
    }

    public void setDigest2(MessageDigest digest2) {
        this.digest2 = digest2;
    }

    public void digest(String msg) {
        System.out.println("digest1 사용");
        digest(msg, digest1);

        System.out.println("digest2 사용");
        digest(msg, digest2);
    }

    private void digest(String msg, MessageDigest digest) {
        System.out.println("사용하는 알고리즘: " + digest.getAlgorithm());
        digest.reset();
        byte[] bytes = msg.getBytes();
        byte[] out = digest.digest(bytes);
        System.out.println(out);
    }
}

```

```java
    <bean id="digester" 
        class="com.apress.prospring5.ch4.MessageDigester"
        p:digest1-ref="shaDigest"
        p:digest2-ref="defaultDigest"/>

```

```java
public class MessageDigesterConfigDemo {

    @Configuration
    static class MessageDigesterConfig {

        @Bean
        public MessageDigestFactoryBean shaDigest() {
            MessageDigestFactoryBean factoryOne = new MessageDigestFactoryBean();
            factoryOne.setAlgorithmName("SHA1");
            return factoryOne;
        }

        @Bean
        public MessageDigestFactoryBean defaultDigest() {
            return new MessageDigestFactoryBean();
        }

        @Bean MessageDigester digester() throws Exception {
            MessageDigester messageDigester = new MessageDigester();
            messageDigester.setDigest1(shaDigest().getObject());
            messageDigester.setDigest2(defaultDigest().getObject());
            return messageDigester;
        }
    }

    public static void main(String... args) {
        GenericApplicationContext ctx = new AnnotationConfigApplicationContext(MessageDigesterConfig.class);

        MessageDigester digester = (MessageDigester) ctx.getBean("digester");
        digester.digest("Hello World!");
        ctx.close();
    }
}

```

MessageDigestFactoryBean을 통해 서로 다른 두 빈이 구성됨을 알 수 있음 

![image](https://github.com/user-attachments/assets/2e7d4cd8-26fc-4a37-a548-f47649f97b9e)


### 4.6.2 FactoryBean에 직접 접근하기

빈 이름 앞에 & 문자를 붙여 getBean() 메서드 호출하면 됨

```java
public class AccessingFactoryBeans {

    public static void main(String... args) {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-xml.xml");
        ctx.refresh();
        ctx.getBean("shaDigest", MessageDigest.class);

        MessageDigestFactoryBean factoryBean =
                (MessageDigestFactoryBean) ctx.getBean(**"&shaDigest**");
        try {
            MessageDigest shaDigest = factoryBean.getObject();
            System.out.println(shaDigest.digest("Hello world".getBytes()));
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        ctx.close();
    }
}

```

## 4.7 자바빈 PropertyEditor

프로퍼티 값을 원래 자료 타입에서 String으로 변환 혹은 반대로 String을 원래 타입으로 변환하는 인터페이스

![image](https://github.com/user-attachments/assets/e15fc9e2-513a-4d0c-a2df-1a8720482976)


```java
public class PropertyEditorBean {
    private byte[] bytes;                 // ByteArrayPropertyEditor
    private Character character;          // CharacterEditor
    private Class cls;                    // ClassEditor
    private Boolean trueOrFalse;          // CustomBooleanEditor
    private List<String> stringList;      // CustomCollectionEditor
    private Date date;                    // CustomDateEditor
    private Float floatValue;             // CustomNumberEditor
    private File file;                    // FileEditor
    private InputStream stream;           // InputStreamEditor
    private Locale locale;                // LocaleEditor
    private Pattern pattern;              // PatternEditor
    private Properties properties;        // PropertiesEditor
    private String trimString;            // StringTrimmerEditor
    private URL url;                      // URLEditor

    public void setCharacter(Character character) {
        System.out.println("Character 설정: " + character);
        this.character = character;
    }

    public void setCls(Class cls) {
        System.out.println("Class 설정: " + cls.getName());
        this.cls = cls;
    }

    public void setFile(File file) {
        System.out.println("File 설정: " + file.getName());
        this.file = file;
    }

    public void setLocale(Locale locale) {
        System.out.println("Locale 설정: " + locale.getDisplayName());
        this.locale = locale;
    }

    public void setProperties(Properties properties) {
        System.out.println("읽어들인 Properties 개수 : " + properties.size() + "개");
        this.properties = properties;
    }

    public void setUrl(URL url) {
        System.out.println("URL 설정: " + url.toExternalForm());
        this.url = url;
    }

    public void setBytes(byte... bytes) {
        System.out.println("bytes 설정: " + Arrays.toString(bytes));
        this.bytes = bytes;
    }

    public void setTrueOrFalse(Boolean trueOrFalse) {
        System.out.println("Boolean 설정: " + trueOrFalse);
        this.trueOrFalse = trueOrFalse;
    }

    public void setStringList(List<String> stringList) {
        System.out.println("String 목록 설정. 크기: "
            + stringList.size());

        this.stringList = stringList;

        for (String string: stringList) {
            System.out.println("  String 멤버: " + string);
        }
    }

    public void setDate(Date date) {
        System.out.println("Date 설정: " + date);
        this.date = date;
    }

    public void setFloatValue(Float floatValue) {
        System.out.println("Float 값 설정: " + floatValue);
        this.floatValue = floatValue;
    }

    public void setStream(InputStream stream) {
        System.out.println("Stream 설정: " + stream);
        this.stream = stream;
    }

    public void setPattern(Pattern pattern) {
        System.out.println("Pattern 설정: " + pattern);
        this.pattern = pattern;
    }

    public void setTrimString(String trimString) {
        System.out.println("문자열 trim 설정: " + trimString);
        this.trimString = trimString;
    }

    public static class CustomPropertyEditorRegistrar implements PropertyEditorRegistrar { 
        @Override
        public void registerCustomEditors(PropertyEditorRegistry registry) {
            SimpleDateFormat dateFormatter = new SimpleDateFormat("yyyy/MM/dd");
            registry.registerCustomEditor(Date.class, 
                     new CustomDateEditor(dateFormatter, true));

            registry.registerCustomEditor(String.class, new StringTrimmerEditor(true));
        }
    }

    public static void main(String... args) throws Exception {
        File file = File.createTempFile("test", "txt");
        file.deleteOnExit();

        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/app-context-01.xml");
        ctx.refresh();

        PropertyEditorBean bean = 
            (PropertyEditorBean) ctx.getBean("builtInSample");

        ctx.close();
    }
}

```

![image](https://github.com/user-attachments/assets/4a887640-1c68-463b-a06d-327cb5f7e767)


## 4.8 그 외의 스프링 ApplicationContext 구성 살펴보기

BeanFactory 인터페이스의 구현체는 빈의 초기화, 의존성 주입, 라이프사이클 지원을 제공

ApplicationContext는 BeanFactory 인터페이스를 상속해 다른 유용한 기능들을 제공며 빈 관련 정보를 빈 팩토리보다 훨씬 더 많이 가지고 있음

ApplicationContext를 이용 시 가장 큰 장점은 스프링과 스프링이 관리하는 리소스를 100% 선언적인 방식으로 구성하고 관리가 가능

- 국제화
    - i18n 지원
    - MessageSource 인터페이스를 사용 시 애플리케이션에서 메시지라 불리는 다양한 언어로 저장된 String 리소스에 접근 가능
- 이벤트 발행
    - 중개자로서 이벤트를 발행하고 수신 가능
- 리소스 관리 및 접근
- 추가적인 라이프사이클 인터페이스
- 개선된 스프링 인프라스트럭처 컴포넌트 자동 구성

### 4.8.4 애플리케이션 이벤트

이벤트는 java.util.EventObject를 상속한 ApplicationEvent에서 파생된 클래스

모든 빈은 ApplicationListener<T> 인터페이스를 구현해 이벤트를 받을 수 있음

이벤트는 ApplicationEventPublisher.publishEvent() 메서드로 발행하며 ApplicationContext가 ApplicationEventPublisher 인터페이스를 상속하므로 이벤트를 발행하는 클래스는 반드시 ApplicationContext에 접근할 수 있어야 함

웹 어플리케이션에서는 스프링 프레임워크 클래스가 갖고 있는 protected 메서드를 호출해 ApplicationContext에 접근 가능

```java
public class MessageEvent extends ApplicationEvent {
    private String msg;

    public MessageEvent(Object source, String msg) {
        super(source);
        this.msg = msg;
    }

    public String getMessage() {
        return msg;
    }
}

```

ApplicationEvent 를 상속 받은 MessageEvent는 생성자에서 이벤트 소스의 참조를 전달 받음

```java
public class Publisher implements ApplicationContextAware {
    private ApplicationContext ctx;

    public void setApplicationContext(ApplicationContext applicationContext)
            throws BeansException {
        this.ctx = applicationContext;
    }

    public void publish(String message) {
        ctx.publishEvent(new MessageEvent(this, message));
    }

    public static void main(String... args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext(
                "classpath:spring/app-context-xml.xml");

        Publisher pub = (Publisher) ctx.getBean("publisher");
        pub.publish("세상에 SOS를 보낸다... ");
        pub.publish("...누군가가 병에 담긴...");
        pub.publish("... 이 메시지를 받았으면 좋겠다.");
    }
}

```

Publisher 클래스가 자신의 인스턴스를 ApplicationContext 에서 가져온 뒤 publish() 메서드를 통해 3개의 MessageEvent를 ApplicationContext에 발행 `ctx.publishEvent(new MessageEvent(this, message));`  `Publisher pub = (Publisher) ctx.getBean("publisher");`

**책 상에 4.8.4.2 이벤트 사용에 대한 고려사항 참고 시에 이해가 쉽습니다(아래 내용 주요)**

이벤트는 보통 빠르게 실행되며 애플리케이션의 주 로직 일부가 아닌 반응 로직에서 사용

장시간 사용되며 주 비즈니스 로직의 일부를 담당하는 처리를 할 때 JMS나 RabbitMQ같은 메시징 시스템을 사용할 것을 권장

JMS를 사용하는 주요 장점은 장시간 실행되는 프로세스에 더 적합하다는 점과 시스템에 커짐에 따라 필요한 경우 JMS 기반 비즈니스 메시지 처리 작업을 별도 서버로 구분 가능

## 4.9 리소스 접근하기

org.springframework.core.io.Resource 인터페이스를 사용해 리소스에 접근할 수 있는 기능을 제공

Resource 인터페이스는 10개의 메서드를 정의

contentLength(), exist(), getDescription(), getFile(), getFileName(), getURI(), isOpen(), isReadable(), lastModified(), createRelatove() (이미 생성된 인스턴스에서 상대 경로를 사용해 새로운 Resource 인스턴스를 생성하는 메서드)

## 4.10 자바 클래스를 사용한 구성

XML과 프로퍼티 파일 대신 자바 클래스로 스프링의 ApplicationContext를 구성할 수 있으며 이는 스프링 3.0에 코어로 추가되었다.

## 4.11 프로파일

특정 프로파일이 활성화 시에 스프링은 해당 프로파일에 정의된 ApplicationContext 인스턴스만 구상

### 4.11.1 스프링 프로파일 기능 사용 예제

```java
package com.apress.prospring5.ch4;

public class Food {
    private String name;

    public Food() {
    }

    public Food(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

```java
package com.apress.prospring5.ch4;

import java.util.List;

public interface FoodProviderService {
    List<Food> provideLunchSet();
}

```

```java
public class FoodProviderServiceImpl implements FoodProviderService {
    @Override
    public List<Food> provideLunchSet() {
        List<Food> lunchSet = new ArrayList<>();
        lunchSet.add(new Food("Coke"));
        lunchSet.add(new Food("Hamburger"));
        lunchSet.add(new Food("French Fries"));

        return lunchSet;
    }
}

```

두 구성 파일의 <beans> 태그에 profile을 각각 유치원과 고등학교로 줌

```java
public class FoodProviderServiceImpl implements FoodProviderService {
    @Override
    public List<Food> provideLunchSet() {
        List<Food> lunchSet = new ArrayList<>();
        lunchSet.add(new Food("Milk"));
        lunchSet.add(new Food("Biscuits"));

        return lunchSet;
    }
}

```

```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd"
        profile="highschool">

    <bean id="foodProviderService"
      class="com.apress.prospring5.ch4.highschool.FoodProviderServiceImpl"/>
</beans>
```

```java
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd"
        profile="kindergarten">

    <bean id="foodProviderService" 
      class="com.apress.prospring5.ch4.kindergarten.FoodProviderServiceImpl"/>
</beans>

```

```java
public class ProfileXmlConfigDemo {
    public static void main(String... args) {
        GenericXmlApplicationContext ctx = new GenericXmlApplicationContext();
        ctx.load("classpath:spring/*-config.xml");
        ctx.refresh();

        FoodProviderService foodProviderService =    
            ctx.getBean("foodProviderService", FoodProviderService.class);

        List<Food> lunchSet = foodProviderService.provideLunchSet();

        for (Food food: lunchSet) {
            System.out.println("Food: " + food.getName());
        }

        ctx.close();
    }
}

```

해당 테스트코드 결과로 나온 것은 JVM 인수에 따라 다름Dspring.profiles.active=”kindergarten”를 전달한다며 출력 결과로 Milk와 Biscuits이 출력되며 이는 공급자 구현체가 생산하는 점심 세트와 일치함

### 4.11.3 프로파일 사용 시 고려사항

개발자는 각기 다른 환경에 대한 모든 구성 내용을 애플리케이션 구성 파일이나 자바 클래스에 모두 집어넣고 함께 번들링하는 작업을 주의해서 하지 않으면 애플리케이션에서 오류가 발생하기 쉬움

## 4.12 Environment와 PropertySource 추상화

활성화한 프로파일을 설정하려면 Environment 인터페이스에 접근해야함

Environment 인터페이스는 스프링 애플리케이션 실행 환경을 캡슐화하는 추상화 레이어로 프로파일와 프로퍼티가 주 요인임

프로퍼티는 애플리케이션 폴더나 데이터베이스 접근 정보 등과 같은 애플리케이션 전반적인 환경 구성을 저장하는데 사용함

스프링의 Environment와 PropertySource 추상화 기능은 개발자가 실행환경에서 다양한 구성정보에 접근하는 데 도움을 줌

ApplicationContext를 부트스트랩할 때 채우는 모든 시스템 프로퍼티, 환경변수, 애플리케이션 프로퍼티가 Environment 인터페이스에 의해 제공 됨

## 4.13 JSR-330 애너테이션을 사용한 구성

JEE 6은 JSR-330(자바에서 의존성 주입) 관련 기능을 제공

JSR-330은 JEE 컨테이너나 다른 IoC 호환 프레임워크 내에서 애플리케이션의 DI 구성을 나타내는 데 사용되는 애너테이션으로 이루어짐

JSR-330 애너테이션을 사용 시에 스프링이 아닌 컨테이너로 애플리케이션을 이식할 때 부담을 덜 수 있음

## 4.14 그루비를 사용한 구성

스프링 프레임워크 4.0부터 새롭게 도입된 기능 중 하나는 그루비 언어를 이용해 진을 정의하고 ApplicationContext를 구성하는 것

이는 XML이나 애너테이션 기반 빈 구성 작업을 대체하거나 보조할 수 있는 또 다른 선택 수단을 제공

GenericGroovyApplicaitonContext 클래스를 사용하면 그루비 스크립트에서 스프링 ApplicationContext를 바로 읽어들이거나 자바에서 해당 그루비 스크립트의 구성을 사용할 수 있음

## 4.15 스프링 부트

스프링 부트는 XML, 애너테이션, 자바 구성 클래스, 그루비 스크립트 등에 대한 기초 지식 없이도 스프링 애플리케이션을 구성할 수 있게끔 도와줌

스프링 부트는 사용자가 필요한 의존성을 직접 추측해서 모으는 작업을 없애고 metrics나 health check처럼 대부분 애플리케이션이 필요로 하는 몇 가지 공통 기능을 제공

XML 기반 구성이 전현 필요 없음





