---
title: 스프링 빈
date: 2024-05-25 13:13:26 +0900
categories:
  - Spring
tags:
  - bean
---
# 개념 정리
## 스프링 컨테이너
![](https://velog.velcdn.com/images/melimelame/post/0f9a93d2-e18d-4246-90c9-8773c1a7922d/image.png)
- = spring context = IOC container
- 스프링 빈과 수명 주기 관리
- Java classes + Config -> IOC Container에 전달 -> Ready System

### 1) Bean Factory
- 기본 Spring 컨테이너

### 2) Application Context
- 엔터프라이즈 전용 기능이 있는 고급 Spring container (Advanced Spring Container with enterprise-specific features)
- 웹 애플리케이션 구축, 국제화 기능이 필요한 경우, 혹은 Spring AOP와 잘 통합되도록 할 때 사용 -> 가장 많이 사용하는 컨테이너. 처음 이 프로젝트 시작할 때도 `AnnotationConfig**ApplicationContext**`를 사용했음!
- 웹 어플리케이션, 웹 서비스, REST API, 마이크로서비스 등에 활용하길 추천함
<br>
<br>
## Java Bean vs POJO vs Spring Bean

### 1) POJO (Plain Old Java Object)
- 모든 Java 객체. 아무런 제약 조건 x.

### 2) Java Bean
- EJB(Enterprise Java Bean) : Java Bean이라는 개념 처음 도입
- Java 빈의 조건
	1) 인수 생성자가 없어야 함. (public no-arg constructor)
	2) Getter, Setter가 있어야 함
    3) `Serializable` 인터페이스 구현
- 이 조건들을 만족하면 모두 Java Bean임. 다만 현재는 EJB를 거의 사용하지 않으므로 해당 조건은 유명무실한 상태.

### 3) Spring Bean
- Spring 프레임워크에서 관리하는 것은 모두 Spring Bean이 될 수 있음. IOC 컨테이너가 관리하는 모든 객체는 Spring Bean.

<br>

# Spring Bean 생성하기
![](https://velog.velcdn.com/images/melimelame/post/f6fcf83f-13ca-43e8-a2f6-6e63b2ad5451/image.png)

JVM 내부에서 Spring context를 생성하고, `name`이라는 이름을 가진 Bean을 생성해보려고 한다.
<br>

```java
public class App02HelloWorldSpring {

    public static void main(String[] args) {
		
        // (1)
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(
                HelloWorldConfiguration.class);
                
        // (2) 

        // (3)
        System.out.println(context.getBean("name"));

    }

}
```
(1) 제일 먼저 할 일은 Spring Context를 실행시키는 일이다. configuration 클래스를 추가해 spring context를 실행시키고자 한다. `AnnotationConfigApplicationContext`를 이용해 JVM 안에서 Spring context를 실행시켰다.
<br>
<br>

```java
@Configuration
public class HelloWorldConfiguration {

    @Bean
    public String name() {
        return "GilDong Hong";
    }

}
```
(2) Spring으로 관리할 범위를 설정해준다. `HelloWorldConfiguration` 이라는 클래스를 만들고, `@configuration` 어노테이션을 달아줬다. 추가로 `name()`이라는 메서드도 Bean으로 등록해줬다.

(3) Spring이 관리하는 Bean 확인
Bean은 여러 방법으로 확인 가능하다. 여기서는 `context.getBean("빈의 이름");`으로 확인했다. 
<br>
# 여러 Bean 만들기
```java
record Person(String name, int age) {};		// (1)

record Address(String firstLine, String city) {};

@Configuration
public class HelloWorldConfiguration {

    @Bean
    public String name() {
        return "GilDong Hong";
    }

    @Bean
    public int age() {
        return 15;
    }

    @Bean
    public Person person() {
        return new Person("CheolSoo Kim", 20);
    }

    @Bean(name = "address2")		// (2)
    public Address address() {
        return new Address("Baker Street", "London");
    }

}
```
당연히 객체도 Bean으로 만들 수 있다.
<br>

```java
public class App02HelloWorldSpring {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(
                HelloWorldConfiguration.class);
                
        System.out.println(context.getBean("name"));	// GilDong Hong
        System.out.println(context.getBean("person"));	// Person[name=CheolSoo Kim, age=20]
        System.out.println(context.getBean("address2"));	// Address[firstLine=Baker Street, city=London]
        System.out.println(context.getBean(Address.class));	// (3) Address[firstLine=Baker Street, city=London]

    }

}
```
<br>
## (1) `record`
Java Bean을 간편하게 생성할 수 있게 함. Getter, Setter, 생성자, equals, hashcode, toString 등을 자동으로 만들어줌. Java 16부터 추가. 

## (2) Bean 이름 설정
기본적으로 Bean의 이름은 메서드의 이름을 그대로 사용한다. 그래서 제일 위에서 `context.getBean("name")`으로 `name()` 메서드를 바로 찾을 수 있었다. 그래도 모종의 이유로 메서드와 Bean의 이름을 다르게 설정해야 할 수 있다. 그럴 때는 Bean 어노테이션의 속성으로 name을 기입해주면 된다. `@Bean(name = "설정할 이름")`. 이 때는 `context.getBean("address")`를 실행하면 해당 Bean을 찾을 수 없어 오류가 발생한다.
<br>
## (3) Bean을 검색하는 방법
Spring이 Bean을 관리하기 시작하면 빈 이름 외에 유형으로도 검색이 가능하다. 예를 들어, `context.getBean(Address.class)`로도 동일한 결과를 얻을 수 있다. 다만 Address 클래스를 사용하는 빈이 2개 이상이 되면 오류가 발생할 수 있다.
<br>
<br>

# 기존 Bean을 활용해 새로운 Bean 생성하기
```java
@Configuration
public class HelloWorldConfiguration {

    @Bean
    public String name() {
        return "GilDong Hong";
    }

    @Bean
    public int age() {
        return 15;
    }

    @Bean
    public Person person() {
        return new Person("CheolSoo Kim", 20, new Address("Main Street", "Utrecht"));
    }

    @Bean
    public Person person2MethodCall() {			// (1)
        return new Person(name(), age(), address());
    }

    @Bean
    public Person person3Parameters(String name, int age, Address address2) {		// (2)
        return new Person(name, age, address2);
    }

    @Bean(name = "address2")
    public Address address() {
        return new Address("Baker Street", "London");
    }

}
```

(1) 메서드를 직접 호출해 새로운 빈을 생성함
(2) 기존 Bean을 호출해 사용. Bean의 이름을 활용해 가져다 쓰기 때문에 Address의 매개변수는 모두 `address2`가 된 상태.


# `@Primary` vs `@Qualifier`
```java
@Component @Primary
class QuickSort implement SortingAlgorithm {}

@Component
class BubbleSort implement SortingAlgorithm {}

@Component @Qualifier("RadixSortQualifier")
class RadixSort implement SortingAlgorithm {}

@Component
class ComplexAlgorithm

@Autowired
private SortingAlgorithm algorithm;

@Component
class AnotherComplexAlgorithm

@Autowired @Qualifier("RadixSortQualifier")
private SortingAlgorithm iWantToUseRadixSortOnly;
```

- `@Primary`는 특정 조건이 없는 한 이 어노테이션이 붙은 게 우선으로 적용. 이 경우 QuickSort.
- `@Qualifer`는 구체적으로 지시된 빈을 주입. `iWantToUseRadixSortOnly`는 RadixSort를 주입 받는다.
두 어노테이션을 사용할 때는 사용하는 클래스 입장에서 생각할 것. 만일 RadixSort에 `@Qualifer`를 붙이지 않았다면 빈 이름을 활용해 `@Qualifier(radixSort)`로도 주입 가능하다.
