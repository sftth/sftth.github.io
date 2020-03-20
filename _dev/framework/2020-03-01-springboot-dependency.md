---
title: "Springboot 기초 - 3부 원리"
date: 2020-01-29 11:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

Springboot 소개


**Info Notice:** 이 포스트는 Inflearn 온라인 학습 "스프링 부트 개념과 활용"에서 참고할 사항을 도출하여 빠르게 참고하고자 정리한 블로그입니다.
{: .notice--info}

## 1.의존성 관리 이해 

- 의존성 관리 참고

[using-boot-dependency-management](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-dependency-management)


- pom.xml에 기본적으로 **spring-boot-starter-web**, **spring-boot-starter-test** 설정만 수행
- 버전 명시하지 않음, 명시하지 않은 의존성도 추가 된 것을 확인 할 수 있음
- spring-boot-starter-parent의 parent인 spring-boot-dependencies의 dependencymanagement 영역에 정의되어 있음
- 이를 통해 의존성 관리 부담을 줄여 줌
- spring-boot-starter-parent 사용하고 싶지 않을 때 아래와 같이 설정 가능

```sh 
<dependencyManagement>
		<dependencies>
		<dependency>
			<!-- Import dependency management from Spring Boot -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.0.3.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

- 이 경우 spring-boot-starter-parent에 미리 설정된 dependency는 사용 할 수 없음

## 2. 의존성 관리 응용

### 2.1 버전관리 해주는 의존성 추가

- dependency 설정에 <version> 명시 하지 않음
- spring-boot-starter-parent에 설정된 기본 설정을 사용하겠다는 의

```sh 
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 2.2 버전관리 안 해주는 의존성 추가

- <version> 명시 함

```sh 
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>2.1.0</version>
</dependency>
```

### 2.3 버전 정보 변경 

- spring-boot-starter-parent에 설정된 property를 프로젝트 pom.xml에 설정하여 overriding 함.

```sh 
<properties>
  <spring.version>5.0.6.RELEASE</spring.version>
</properties>
```

## 3. 자동 설정 이해

### 3.1 @SpringBootApplication

- main application class에 보면 @SpringBootApplication annotation을 볼 수 있음 

```java 
@SpringBootApplication
public class WhmsApplication {
...
```

- @SpringBootApplication은 @springBootConfiguration, @ComponentScan, @EnableAutoConfiguration 역할 수행 
- 결국 빈을 만들기 위한 스캔 작업을 수행하는데 두 단계로 나눠서 순서대로 수행 함.
   - 1단계: @ComponentScan
   - 2단계: @EnableAutoConfiguration

### 3.2 @EnableAutoConfiguration 
- @SpringBootApplication 안에 숨어 있음
- springboot가 webapplication으로 구동 되는데 필요한 설정을 자동으로 스캔하여 bean으로 등록함
- 따라서 @EnableAutoConfiguration을 주석 처리하고 Springboot를 기동하면 Error 발생 

**참고** 아래와 같이 Springboot를 SpringApplication을 선언하여 webapplication이 아닌 유형으로 동작하면 기동 가능
{: .notice--info}

```java
@Configuration
@Component
public class Application {
  public static void main(String[] args) {
    SpringApplication application = new SpringApplication(Application.class);
    application.setWebApplicationType(WebApplicationType.NONE);
    application.run(args);
  }
}
```

- 하지만 Springboot를 webapplication으로 구동하려면 @EnableAutoconfiguration이 필요함
- 결국 @SpringBootApplication 설정을 하면 springboot를 webapplication으로 구동 가능하고 이 때 scan 순서는
componentscan, autoconfiguration을 읽음 

### 3.3 @ComponentScan

- 아래 annotation이 달린 클래스를 스캔하여 빈으로 등록
   - @Component
   - @Configuration @Repository @Service @Controller @RestController
- 물론 스캔 영역이 아닌 패키지에 있는 경우는 스캔하지 않음.


### 3.4 @EnableAutoConfiguration 상세
- spring-boot-autoconfigure lib 하위 META-INF의 spring.factories 읽음
- spring.factories 에 org.springframework.boot.autoconfigure.EnableAutoConfiguration 설정 확인 
- spring.factories 안에 있는 설정을 읽게 되고 설정에 @Configuration을 빈으로 등록함.
- @ConditionalOnXxxYyyZzz은 조건에 따라 빈으로 등록 함.

## 4. 자동설정 만들기
- @EnableAutoConfiguration 이해를 위한 예제 프로젝트 제작

### 4.1 pom.xml 설정

```xml 
<dependencies>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-autoconfigure</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-autoconfigure-processor</artifactId>
      <optional>true</optional>
  </dependency>
</dependencies>

<dependencyManagement>
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-dependencies</artifactId>
          <version>2.0.3.RELEASE</version>
          <type>pom</type>
          <scope>import</scope>
      </dependency>
  </dependencies>
</dependencyManagement>
```

### 4.2 클래스 생성

- Holoman Class

```java
public class Holoman {
    
    String name;
    int howLong;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getHowLong() {
        return howLong;
    }

    public void setHowLong(int howLong) {
        this.howLong = howLong;
    }

    @Override
    public String toString() {
        return "Holoman{" +
                "name='" + name + '\'' +
                ", howLong=" + howLong +
                '}';
    }
}
```

- Holoman configuration class

```java 
package sftth.springboot;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HolomanConfiguration {
    @Bean
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setName("Jacob");
        holoman.setHowLong(5);
        return holoman;
    }
}
```

### 4.3 spring.factories 생성
- resources/META-INF 디렉토리 생성
- META-INF 하위에 spring.factories 파일 생성
- spring.factories에 EnableAutoConfiguration 설정

```xml 
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  sftth.springboot.HolomanConfiguration
```
### 4.4 maven install 수행



