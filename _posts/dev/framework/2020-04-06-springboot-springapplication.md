---
title: "Springboot #3 활용 - springApplication"
date: 2020-04-06 00:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

### 1. SpringApplication 1

#### 1.1 Reference Page

[Spring Boot Features](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-application)

#### 1.2 Springboot 로그
- Springboot 기본 로그 레벨 INFO
- Springboot 로그 변경은 여러 방법이 있지만 Intellij 환경에서는 
Run/Debug Configurations에서 VM Option이나 Program Argument 옵션 설정으로
변경 가능 
```xml
VM Options : -Ddebug
program arguments: --debug
```

- Springboot는 디버그 모드 로그에서 다양한 FailureAnalyzer 제공

#### 1.3 Springboot 배너
- 배너 변경은 resources 하위에 banner.txt | gif | jpg | png 저장 또는
application.properties에 classpath 또는 spring.banner.location 등록
- Banner 클래스 구현하고 SpringApplication.setBanner()로 설정 가능.
- 배너 끄는 방법은 application 객체에 setBanner(Mode.off) 설정
- SpringApplicationBuilder로 빌더 패턴 사용 가능

### 2. ApplicationEvent 등록

- Springboot ApplicationContext를 설정 전 뭔가 처리하고자 할 때 아래 listener 구현 

```java
public class SampleListener implements ApplicationListener<ApplicationStartingEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartingEvent applicationStartingEvent) {
        System.out.println("=======================");
        System.out.println("Application is starting");
        System.out.println("=======================");
    }
}
```

- 위 구현된 리스너의 로그를 출력하려면 @Component 스캔으로 안 되고 아래처럼 application에 
매개변수로 할당 해야 함.

```java
@SpringBootApplication
public class SpringbootBasicApplication {

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(SpringbootBasicApplication.class);
        app.addListeners(new SampleListener());
        app.run(args);
    }

}
```

- application 컨텍스트 생성 후 로그 출력을 위한 리스너 

```java
@Component
public class SampleStartedListener implements ApplicationListener<ApplicationStartedEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartedEvent applicationStartedEvent) {
        System.out.println("=======================");
        System.out.println("Application is started.");
        System.out.println("=======================");
    }
}
```
- springboot arguments 확인 방법1 ApplicationArguments 사용 

```java
@Component
public class SampleArgumentPrinter {
    public SampleArgumentPrinter(ApplicationArguments arguments) {
        System.out.println("foo: " + arguments.containsOption("foo"));
        System.out.println("bar: " + arguments.containsOption("bar"));
    }
}
```

- springboot arguments 확인 방법2 ApplicationRunner 사용 

```java
@Component
public class SampleApplicationRuner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("foo: " + args.containsOption("foo"));
        System.out.println("bar: " + args.containsOption("bar"));
    }
}
```

- springboot arguments 확인 방법3 CommandLineRunner 사용 

```java
@Component
public class SampleApplicationRuner implements CommandLineRunner {


    @Override
    public void run(String... args) throws Exception {
        Arrays.stream(args).forEach(System.out::println);
    }
}
```
