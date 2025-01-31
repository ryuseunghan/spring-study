## 1. IoC와 DI

### IoC(Inversion of Control)란?

- IoC는 객체의 생성 및 의존성 관리를 개발자가 아닌 **컨테이너**가 수행하는 개념이다.
- 객체 간의 **결합도를 낮추고 유연성을 높이는 방식**으로, 애플리케이션의 확장성과 유지보수성을 개선한다.
- IoC의 대표적인 구현 방식은 다음과 같다:
    - **의존성 주입(DI, Dependency Injection)**: 객체가 직접 의존성을 찾는 것이 아니라, 외부에서 주입받는 방식.
    - **의존성 룩업(DL, Dependency Lookup)**: 객체가 직접 IoC 컨테이너에서 필요한 의존성을 가져오는 방식.

### DI(Dependency Injection)란?

- DI는 IoC의 한 구현 방식으로, **의존성 객체를 코드 내에서 직접 생성하지 않고 컨테이너가 주입하는 방식**.
- DI를 통해 **객체 간의 강한 결합을 피하고, 모듈화와 테스트 용이성을 향상**할 수 있다.
- DI의 방식에는 다음과 같은 유형이 있다:
    - **생성자 주입(Constructor Injection)**
    - **수정자 주입(Setter Injection)**
    - **필드 주입(Field Injection) (권장되지 않음)**

---

## 2. IoC 컨테이너의 역할

- **Bean의 생성 및 관리**: IoC 컨테이너는 애플리케이션 실행 시 빈(Bean)을 생성하고 관리한다.
- **의존성 자동 주입**: 필요한 의존성을 자동으로 연결해준다.
- **라이프사이클 관리**: 객체의 생성부터 소멸까지의 라이프사이클을 제어한다.

### 2.1 BeanFactory와 ApplicationContext

| IoC 컨테이너 유형 | 설명 |
| --- | --- |
| BeanFactory | 가장 기본적인 IoC 컨테이너, 지연 초기화(Lazy Initialization) 지원 |
| ApplicationContext | BeanFactory를 확장한 인터페이스, 추가 기능 제공 (이벤트 처리, 국제화 지원 등) |

---

## 3. 의존성 주입 방식

### 3.1 생성자 주입 (Constructor Injection)

- **객체 생성 시 의존성을 생성자를 통해 주입**하는 방식.
- 장점: 객체가 생성될 때 반드시 필요한 의존성을 설정할 수 있어 **불변성을 유지**할 수 있음.
- 단점: 의존성이 많아질 경우 생성자의 파라미터가 너무 많아질 수 있음.

### 예제 코드

```
@Component
public class ConstructorInjection {
    private final Dependency dependency;

    @Autowired
    public ConstructorInjection(Dependency dependency) {
        this.dependency = dependency;
    }
}
```

### 3.2 수정자 주입 (Setter Injection)

- **객체 생성 후 setter 메서드를 통해 의존성을 주입**하는 방식.
- 장점: **객체 생성 후에 유연하게 변경 가능**.
- 단점: 객체가 완전히 생성된 후에야 의존성이 주입되므로, **불완전한 상태의 객체가 존재할 가능성**이 있음.

### 예제 코드

```
@Component
public class SetterInjection {
    private Dependency dependency;

    @Autowired
    public void setDependency(Dependency dependency) {
        this.dependency = dependency;
    }
}
```

### 3.3 필드 주입 (Field Injection)

- **필드에 직접 @Autowired 어노테이션을 사용하여 주입하는 방식**.
- 장점: 코드가 간결해짐.
- 단점: **의존성 주입이 강제되지 않으며**, 테스트가 어려워 유지보수성이 낮음.

### 예제 코드 (비권장)

```
@Component
public class FieldInjection {
    @Autowired
    private Dependency dependency;
}
```

---

## 4. 빈 스코프 설정

| 스코프 유형 | 설명 |
| --- | --- |
| Singleton | 기본 스코프. 하나의 인스턴스만 생성됨 |
| Prototype | 요청할 때마다 새로운 인스턴스를 생성함 |
| Request | 요청 단위로 빈을 생성 (웹 애플리케이션) |
| Session | 사용자 세션 단위로 빈을 생성 |
| GlobalSession | 글로벌 세션 단위로 빈을 생성 |
| Thread | 요청마다 새로운 인스턴스를 생성 (멀티스레드 환경에서 사용) |

### 예제 코드

```
@Component
@Scope("prototype")
public class PrototypeBean {}
```

---

## 5. 빈 이름 및 별칭 설정

### 5.1 @Component 어노테이션을 이용한 빈 등록

- `@Component("beanName")`을 사용하여 특정 이름으로 빈을 등록할 수 있음.

### 예제 코드

```
@Component("customBeanName")
public class MyComponent {}
```

### 5.2 @AliasFor 어노테이션을 이용한 빈 별칭 설정

- `@AliasFor`를 사용하면 다른 애너테이션에서 같은 속성을 공유할 수 있음.

### 예제 코드

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Award {
    @AliasFor("value")
    String prize() default "";

    @AliasFor("prize")
    String value() default "";
}
```

### 5.3 @Qualifier를 사용한 특정 빈 지정

- 같은 타입의 여러 빈이 존재할 때 특정 빈을 주입할 때 사용.

### 예제 코드

```
@Component
public class ServiceA {}

@Component
public class ServiceB {}

@Component
public class Consumer {
    private final ServiceA serviceA;

    @Autowired
    public Consumer(@Qualifier("serviceA") ServiceA serviceA) {
        this.serviceA = serviceA;
    }
}
```

---

## 6. 빈 상속 설정

- 스프링에서는 `<bean parent="...">`를 사용하여 상속 개념을 적용할 수 있음.
- 부모 빈의 설정을 자식 빈이 그대로 물려받을 수 있으며, 일부 속성을 재정의 가능함.

### XML 설정 예제

```
<bean id="parent" class="com.example.Singer" p:name="John Mayer" p:age="39"/>
<bean id="child" class="com.example.Singer" parent="parent"/>
```

---

## 7. 결론

- **IoC는 객체의 생성과 의존성 관리를 컨테이너가 대신 수행하는 개념**.
- **DI는 IoC의 한 형태로, 객체가 직접 의존성을 찾지 않고 외부에서 주입받는 방식**.
- **빈 스코프 설정을 통해 애플리케이션 요구사항에 맞게 빈의 라이프사이클을 관리할 수 있음**.
- **빈 이름 및 별칭을 설정하여 애플리케이션 내에서 빈을 효과적으로 활용 가능**.
- **@Qualifier, @AliasFor, @DependsOn 등의 어노테이션을 활용하여 더욱 유연하게 빈을 관리할 수 있음**.

## 📌 참고

- "전문가를 위한 스프링 5" (개정 5판)
