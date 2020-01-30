---
title: "Springboot Logback 설정"
date: 2020-01-28 11:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---
springboot 환경에서 Log4j2는 slf4j 구현체 매칭 작업에 어려움이 있다. 단적으로 pom.xml에 slf4j를 설정하면 slf4j 인터페이스 구현체 설정이 되지 않아서 로그가 남지 않는 문제가 발생한다. 반면에 Logback은 springboot에서 기본적으로 제공되어 설정이 상대적으로 간편하다. 이번 포스팅은 그 간편한 Logback을 활용한 로그 작성 방법에 관한 것이다.

## 기반
- 포스팅의 기반되는 프로젝트는 maven 기반 springboot 프로젝트.

## pom.xml
- springboot의 pom.xml에 기본 설정된 logback 관련 dependency 확인.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

- spring-boot-starter-web 하위에 logback 과 slf4j 관련 dependency가 포함되어 있음

  ![logback](/assets/images/springboot-logback001.png)

- 참고로 slf4j는 로깅퍼사드(Facade)로 로거 API의 인터페이스 임.
- 로깅 퍼사드는 로거의 선택을 자유롭게 사용할 수 있게 함.
- 즉, 로깅 퍼사드를 slf4j로 하고 로거를 log4j2, logback 등으로 자유롭게 변경 가능함.
- 스프링부트 로깅의 기본 매커니즘은 Commons Logging > SLF4j > Logback 호출 관계를 갖고 최종적으로 Logback이 로그를 남김 

## 소스
- Logback 검증을 위한 소스를 구현

    ```java
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    ...

    @Controller
    public class SignInController {
        private static final Logger LOGGER = LoggerFactory.getLogger(SignInController.class);

        @GetMapping("/signIn")
        public String signInView(Model model) {
            LOGGER.info("signInView is called");
            return "mains/signIn";
        }
    ```

- 소스 구동 결과, 이미 처럼 로그가 생성 됨

  ![logback](/assets/images/springboot-logback002.png)

## Custermizing

## 검증