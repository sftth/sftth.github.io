---
title: "Springboot Logback 설정"
date: 2020-01-29 11:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
toc: true
---
springboot 환경에서 Log4j2는 slf4j 구현체 매칭 작업에 어려움이 있다. 단적으로 pom.xml에 slf4j를 설정하면 slf4j 인터페이스 구현체 설정이 되지 않아서 로그가 남지 않는 문제가 발생한다. 반면에 Logback은 springboot에서 기본적으로 제공되어 설정이 상대적으로 간편하다. 이번 포스팅은 그 간편한 Logback을 활용한 로그 작성 방법에 관한 것이다.

## Base
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

## Code
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

## Test - Default
- 소스 구동 결과, 이미 처럼 로그가 생성 됨

  ![logback](/assets/images/springboot-logback002.png)

## Custermizing
- 콘솔에서 로그 표출은 확인하였으나 이번에는 두 가지 사항을 적용함.
   - 어플리케이션 로그는 파일로 남김
   - 로그 문법을 필요에 맞게 수정 함
- 위 두 가지 사항 적용을 위해 logback-spring.xml 파일을 작성함.
   - 작성 경로: src/main/resources/logback-spring.xml
   - 작성 샘플
   
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <property name="LOG_PATH" value="C:/IDEA/logs" />
   <configuration>
       <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
           <layout class="ch.qos.logback.classic.PatternLayout">
               <Pattern>%d{yyyy-MM-dd HH:mm:ss}:%-3relative][%thread] %-5level %logger{35} - %msg%n</Pattern>
           </layout>
       </appender>
   
       <appender name="appAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <file>${LOG_PATH}/app.log</file>
           <encoder>
               <pattern>[%d{yyyy-MM-dd HH:mm:ss}:%-3relative][%thread] %-5level %logger{35} - %msg%n</pattern>
           </encoder>
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <fileNamePattern>${LOG_PATH}/app.log.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
               <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                   <!-- or whenever the file size reaches 100MB -->
                   <maxFileSize>5MB</maxFileSize>
                   <!-- kb, mb, gb -->
               </timeBasedFileNamingAndTriggeringPolicy>
               <maxHistory>5</maxHistory>
           </rollingPolicy>
       </appender>
      
       <logger name="com.summit.whms" level="INFO">
           <appender-ref ref="appAppender" />
       </logger>
   
       <root level="ERROR">
           <appender-ref ref="appAppender" />
           <appender-ref ref="consoleAppender" />
       </root>
   </configuration>
   ```
  
- logback-spring.xml에 대한 간략한 설명은 아래 표로 정리

  | 구분            | 속성 | 설명                      |
  | ---             | --- | ---                       |
  | consoleAppender | -   | console 화면에 로그를 남김 | 
  |                 | layout | 출력 로그 포맷 설정 |
  |                 | pattern | 출력 로그 포맷 상세 |
  |                 | 예시    | %d{yyyy-MM-dd HH:mm:ss}:%-3relative]|
  |                 |         | 2020-01-30 13:50:23:2544] |
  |                 |         | [%thread]  |
  |                 |         | [main]     |
  |                 |         | %-5level   |
  |                 |         | INFO             |
  |                 |         | %logger{35} -    |
  |                 |         | com.test.package |
  |                 |         | %msg%n           |
  |                 |         | 출력메시지 \개행  |
  | appAppender     | -       | 파일에 로그를 남김|
  |                 | file    | 로그 파일 경로    |
  |                 | encoder | 로그 pattern 설정 |
  |                 | rollingPolicy      | 파일 롤링 정책 설정  |
  |                 | fileNamePattern    | 롤링 파일 생성 포맷  |
  |                 | timeBased...Policy | 시간/용량 기준 파일 롤링 정책 |

## Test - Customizing
- 소스를 재구동 하면 logback customizing 결과를 확인할 수 있음

![logback](/assets/images/springboot-logback003.png)

## Profile
- 로그 생성시 로컬,개발,운영 환경마다 다른 속성을 설정해야 할 필요 있음.
- logback-spring.xml에 아래와 같이 profile 설정으로 해당 요건을 처리 가능

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <springProfile name="win">
            <property name="LOG_PATH" value="C:/IDEA/logs" />
        </springProfile>
        <springProfile name="mac">
            <property name="LOG_PATH" value="/Users/Summit/IDE/dev/logs" />
        </springProfile>
        <springProfile name="dev">
            <property name="LOG_PATH" value="/logs001/sftth318/springboot" />
        </springProfile>
    ... 중략
    ```


- profile을 적용하여 springboot 기동하여 검증
    - intelliJ에서 아래와 같이 설정 후 기동
    
    ![logback](/assets/images/springboot-logback004.png)
    
    - jar 기동시 아래와 같이 설정 후 기동(실행 스프링부트가 test.jar인 경우)
    
    ```shell script
    java -jar -Dspring.profiles.active=dev test.jar
    ```
## Summary
포스팅의 내용 처럼 springboot의 logback은 설정이 용이하고 커스터마이징도 간편하다.
더불어 환경별 동적 속성 변경은 profile 설정을 통해서 해결할 수 있다.     
