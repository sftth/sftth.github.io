---
title: "Springboot #3 활용 - 외부설정"
date: 2020-04-06 01:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

[4.2 Externalized Configuration](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config)

### 1. 외부설정 1부

#### 1.1 외부 설정
- properties
  - application.properties에 key=value 형태로 정의
  - 사용 방법 @Value("${summit.name}")
- YAML
- 환경 변수
- 커맨드 라인 아규먼트

#### 1.2 우선순위

- 유저 홈 디렉토리에 있는 spring-boot-dev-tools.properties
- 테스트에 있는 @TestPropertySource

```sh 
test
+- java
 +- package
  +-xxxxyyyTests
+- resources
 +- application.properties
```

```java
package com.summit.dev.springbootbasic;

import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.Environment;
import org.springframework.test.context.junit4.SpringRunner;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
class SpringbootBasicApplicationTests {

    @Autowired
    Environment environment;

    @Test
    void contextLoads() {
        assertThat(environment.getProperty("sample.name"))
                .isEqualTo("jacob");
    }

}
```
- @SpringBootTest 애노테이션의 properties 애트리뷰트

```java
@RunWith(SpringRunner.class)
@SpringBootTest(properties = "sample.name=bbang")
class SpringbootBasicApplicationTests {
```

- 커맨드 라인 아규먼트

```sh 
  java -jar ./target/springboot-basic-0.0.1-SNAPSHOT.jar --sample.name=yeseo
``` 

- SPRING_APPLICATION_JSON (환경 변수 또는 시스템 프로티) 에 들어있는 프로퍼티
- ServletConfig 파라미터
- ServletContext 파라미터
- java:comp/env JNDI 애트리뷰트
- System.getProperties() 자바 시스템 프로퍼티
- OS 환경 변수
- RandomValuePropertySource
- JAR 밖에 있는 특정 프로파일용 application properties
- JAR 안에 있는 특정 프로파일용 application properties
- JAR 밖에 있는 application properties
- JAR 안에 있는 application properties
- @PropertySource
- 기본 프로퍼티 (SpringApplication.setDefaultProperties)

```properties
sample.name=jacob
```

#### 1.3 application.properties 우선순위

- 1. file:./config/
- 2. file:./
- 3. classpath:/config/
- 4. classpath:/

### 2. 외부설정 2부

#### 2.1 같은 키의 설정을 빈으로 등록

- 같은 키의 설정을 빈으로 등록하는 방법 

```properties
sample.name=jacob
sample.age=${random.int(0, 100)}
sample.fullname=${sample.name} park
```

- @ConfigurationProperties

```java
package com.summit.dev.springbootbasic.sample;

import org.springframework.boot.context.properties.ConfigurationProperties;


@Component
@ConfigurationProperties("sample")
public class SampleProperties {
    private String name;
    private int age;
    private String fullname;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getFullname() {
        return fullname;
    }

    public void setFullname(String fullname) {
        this.fullname = fullname;
    }
}
```

- dependency 추가 

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
```

- @EnableConfigurationProperties

```java
@SpringBootApplication
@EnableConfigurationProperties(SampleProperties.class)
public class SpringbootBasicApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootBasicApplication.class, args);
    }

}
```

- 활용

```java
@Component
public class SampleRunner implements ApplicationRunner {
    @Autowired
    SampleProperties sampleProperties;
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(" =================== ");
        System.out.println( sampleProperties.getName() );
        System.out.println( sampleProperties.getAge() );
        System.out.println(" ===================");
    }
}
```
   
#### 2.2 Relexed binding
- 아래 형식 모두 contextPath에 바인딩 됨

context-path (케밥) <br>
context_path (언드스코어) <br>
contextPath (캐멀) <br>
CONTEXTPATH <br>

#### 2.3 프로퍼티 타입 컨버전
@DurationUnit

```java
@DurationUnit(ChronoUnit.SECONDS)
private Duration sessionTimeout = Duration.ofSeconds(30);

public Duration getSessionTimeout() {
    
    return sessionTimeout;
}

public void setSessionTimeout(Duration sessionTimeout) {
    this.sessionTimeout = sessionTimeout;
}
```

#### 2.4 프로퍼티 값 검증
@Validated <br>
JSR-303 (@NotNull, ...) <br>

```java
@Component
@ConfigurationProperties("sample")
@Validated
public class SampleProperties {
    private String name;
    private int age;
    private String fullname;

    @NotEmpty
    public String getName() {
        return name;
    }
```


메타 정보 생성
@Value
SpEL 을 사용할 수 있지만...
위에 있는 기능들은 전부 사용 못합니다.


### 3. 외부설정 3부
