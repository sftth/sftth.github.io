---
title: "Springboot #3 활용 - springApplication"
date: 2020-04-06 03:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---


### 1. @Profile

- DevConfiguration 생성

```java
@Profile("dev")
@Configuration
public class DevConfiguration {

    @Bean
    public String hello() {
        return "hello dev";
    }
}
```

- ProdConfiguration 생성

```java
@Profile("prod")
@Configuration
public class ProdConfiguration {
    @Bean
    public String hello() {
        return "hello prod";
    }
}
```

- application.properties 설정

```properties
spring.profiles.active=prod
```

- 활용 코드

```java 
@Component
public class SampleRunner implements ApplicationRunner {

    @Autowired
    private String hello;

    @Autowired
    SampleProperties sampleProperties;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(" =================== ");
        System.out.println( sampleProperties.getName() );
        System.out.println( hello );
        System.out.println(" ===================");
    }
}
```

- 결과

```sh 
 
jacob
hello prod

```

### 2. application.properties 구분

- resources 하위에 application.properties 파일을 구분하여 만듬

```sh 
resources
+- application.properties
+- application-prod.properties
+- application-dev.properties
```

- Promgram arguments에 profile 설정

```sh 
--spring.profiles.active=prod
```

- 출력 결과는 application-prod.properties에 설정된 값 출력

### 3. application-{}.properties 포함하기

- application-devdb.properties 작성

```properties
sample.name=dbdbdb
```

- application-dev.properties 파일에 아래 설정 

```properties
spring.profiles.include=devdb
```

- 출력 결과

```sh 
dbdbdb
hello dev
```

- application-devdb.properties 값으로 override 됨
