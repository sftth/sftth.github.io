---
title: "Springboot #2 원리 - 자동 설정의 이해2"
date: 2020-04-05 00:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

Springboot 자동설정 이해 2


**Info Notice:** 이 포스트는 Inflearn 온라인 학습 "스프링 부트 개념과 활용"에서 참고할 사항을 도출하여 빠르게 참고하고자 정리한 블로그입니다.
{: .notice--info}

### 1. 자동설정의 문제점

- [Springboot #2 원리 - 자동 설정의 이해](http://sftth.github.io/springboot-dependency/)에서 수행한 프로젝트에서
아래와 같이 @Bean을 설정하여 dependency 설정된 holoman 클래스의 값을 명시적으로 바꾸면 어떻게 될까?

```java
package com.summit.dev.springboot;

import me.summit.Holoman;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class SpringbootApplication {

    //psvm
    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }

    @Bean
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setName("JS");
        holoman.setHowLong(120);
        return holoman;
    }
}
```

- 결과는 아래 로그 처럼 override 되지 않고 기존의 값이 출력 됨

```bash
....<생략>....
2020-04-05 09:58:22.546  INFO 5508 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-04-05 09:58:22.552  INFO 5508 --- [           main] c.s.d.springboot.SpringbootApplication   : Started SpringbootApplication in 2.97 seconds (JVM running for 3.959)
Holoman{name='Jacob', howLong=5}
```
- 이 문제는 어떻게 해결해야 하나?

**Info Notice:** 참고로 springboot 2.1 부터는 overriding 옵션 기본값이 false로 바뀌어서 application.yml 이나 application.propeties
파일에 spring.main.allow-bean-definition-overriding: true 옵션 추가해 주어야 예제 수행이 가능
{: .notice--info}

### 2. 해결책

- summit-spring-boot-starter에 정의한 HolomanConfiguration에 @ConditionalOnMissingBean을 추가하여 조건에 따라
설정 되도록 

```java
package me.summit;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HolomanConfiguration {
    @Bean
    @ConditionalOnMissingBean
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setName("Jacob");
        holoman.setHowLong(5);
        return holoman;
    }
}
```

- @ConditionalOnMissionBean을 추가한 summit-spring-boot-starter의 pom.xml에 version을 2.0-SNAPSHOT으로
바꾸어 다시 maven install 함

- 참조할 프로젝트의 pom.xml에서 summit-spring-boot-starter의 버전을 2.0-SNAPSHOT바꾸어 결과 확인 

- 실행결과 ovrride 한 내용을 바뀐 것을 확인 

```bash
2020-04-05 10:09:25.679  INFO 5578 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-04-05 10:09:25.685  INFO 5578 --- [           main] c.s.d.springboot.SpringbootApplication   : Started SpringbootApplication in 3.893 seconds (JVM running for 5.733)
Holoman{name='JS', howLong=120}
```

### 3. 또 다른 문제점

- 그럼, 클래스 override 할 때마다 아래처럼 클래스를 새로 생성하지 않고 만들 수는 없는가?

```java
@SpringBootApplication
public class SpringbootApplication {

    //psvm
    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }

    @Bean      ----------------------> 항상 이렇게 해야 하는가?
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setName("JS");
        holoman.setHowLong(120);
        return holoman;
    }
}
```

### 4. 또 다른 문제점의 해결책

- 이 문제 해결은 properties 파일에 있는 값을 읽도록 수정하여 변경이 필요한 경우, properties
파일만 수정하면 되도록 하면 됨

- 먼저, summit-spring-boot-starter의 resources 아래에 application.properties 를 만들고 거기에 아래 내용을 작성함

```properties
holoman.name = hangwn
holoman.howlong = 365
```

- Propeties 활용을 위한 클래스를 아래와 같이 작성

```java
package me.summit;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("holoman")
public class HolomanProerties {
    private String name;
    private int howlong;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getHowlong() {
        return howlong;
    }

    public void setHowlong(int howlong) {
        this.howlong = howlong;
    }
}
```

- 프로퍼티 키-값 자동 완성을 위해 pom.xml에 아래 dependency를 추가

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
```

- Holomanconfiguration에 @EnableConfigurationProperties 설정
- @EnableConfigurationProperties로 가져온 properties 값을 활용하도록 소스 수정 

```java
@Configuration
@EnableConfigurationProperties(HolomanProerties.class)
public class HolomanConfiguration {
    @Bean
    @ConditionalOnMissingBean
    public Holoman holoman(HolomanProerties properties) {
        Holoman holoman = new Holoman();
        holoman.setName(properties.getName()); -------> properties 값 사용
        holoman.setHowLong(properties.getHowlong()); ---> properties 값 사용 
        return holoman;
    }
}
```

- 검증을 위핸 summit-spring-boot-starter-3.0-SNAPSHOT.jar를 만들고 해당 jar를 사용할 다른 프로젝트의 pom.xml에
dependency 설정
- 타 프로젝트의 application.properties에 아래와 같이 설정하여 적용되는지 로그 확인

```properties
holoman.howlong= 12000
holoman.name= holoman-two
```

- 적용 결과 

```bash
2020-04-05 16:40:45.504  INFO 6003 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-04-05 16:40:45.511  INFO 6003 --- [           main] c.s.d.springboot.SpringbootApplication   : Started SpringbootApplication in 3.092 seconds (JVM running for 4.086)
Holoman{name='holoman-two', howLong=12000}
```