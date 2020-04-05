---
title: "Springboot #1 시작 "
date: 2020-03-01 11:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

**Info Notice:** 이 포스트는 Inflearn 온라인 학습 "스프링 부트 개념과 활용"에서 참고할 사항을 도출하여 빠르게 참고하고자 정리한 블로그입니다.
{: .notice--info}

### 1. Springboot 소개

- 우선, Springboot에 대한 공식적인 소개는 
[Springboot 2.0.3.RELEASE doc](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#getting-started-introducing-spring-boot)
링크를 참고합니다.

### 2. Springboot 시작


#### 2.1 Reference Book

- Springboot 시작에서 다루는 내용은 참고 문서
[Reference Doc](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/#getting-started-maven-installation) 
에서 확인 가능합니다.
   
***참고 문서 경로***
   
[Spring.io](https://spring.io) > Projects > Springboot > LEARN > Reference Doc
   
#### 2.2 프로젝트 만들기

다음 springboot 최초 시작 환경은 Intellij 환경으로 가정하여 진행합니다. <br>

- File > New > Proejct 선택

![WHMS](/assets/images/springboot/WHMS52001.png)

- Spring Initializr 선택 > Next

![WHMS](/assets/images/springboot/WHMS52002.png)

- Group, Artifact 입력 > Next

![WHMS](/assets/images/springboot/WHMS52003.png)

- Project name, Project location 입력 > Finish

![WHMS](/assets/images/springboot/WHMS52004.png)

- 최종 생성 완료된 모습 

![WHMS](/assets/images/springboot/WHMS52005.png)

#### 2.3 의존성 설정

의존성 설정은 maven pom.xml에 필요한 의존 관계를 설정하는 작업을 말합니다. <br>
 
- 먼저, pom.xml 파일에 springboot parent 설정을 수행합니다.
- parent 설정은 앞으로 springboot 의존성 관리의 자동 설정에 대한 내용과 밀접한 관련을 갖습니다.

```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.3.RELEASE</version>
</parent>
```

- springboot start web 의존성을 추가합니다.

```xml 
<!-- Add typical dependencies for a web application -->
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>
```

- 마지막으로 빌드 플러그인을 추가합니다.

```xml 
<!-- Package as an executable jar -->
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```

- 기본적인 설정이 최종 완료된 pom.xml의 내용은 아래와 같습니다.

```xml 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>

<groupId>com.summit</groupId>
<artifactId>whms</artifactId>
<version>1.0.0</version>

<!-- Inherit defaults from Spring Boot -->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.3.RELEASE</version>
</parent>

<!-- Add typical dependencies for a web application -->
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>

<!-- Package as an executable jar -->
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>

</project>
```

#### 2.4 어플리케이션 작성

- Springboot 구동을 위해 간단한 어플리케이션을 작성하여 구동하겠습니다.
- 어플리케이션은 src/main/java/com.summit.dev.springboot 하위 만듭니다.
- Spring Initializr로 만든 경우 이미 해당 경로에 artifact id로 생성 된 클래스파일 있으니 해당 파일을 활용합니다.

![WHMS](/assets/images/springboot/WHMS52006.png)
   
```java 
package com.summit.dev.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringbootApplication {

    //psvm
    public static void main(String[] args) {        
        SpringApplication.run(SpringbootApplication.class, args);
    }

}
```

### 3. 기동 및 확인 

- 지금까지 작성한 내용을 기본으로 springboot 프로젝트를 기동하여 동작을 확인 합니다.

- 작성한 클래스에서 마우스 우 클릭하여 Run 수행

![WHMS](/assets/images/springboot/WHMS52007.png)

- 수행 후 8080으로 Tomcat 기동 관련 로그 확인 

![WHMS](/assets/images/springboot/WHMS52008.png)

- 브라우저에서 http://localhost:8080 으로 접속 확인 

![WHMS](/assets/images/springboot/WHMS52009.png)

여기 까지 확인이 완료되면 기본적인 springboot 프로젝트 생성이 완료 된 것입니다.
  
### 4. Springboot 프로젝트 구조

- [3.2 Structuring Your Code 참고](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-structuring-your-code)
- Springboot 프로젝트는 일반적인 maven, gradle 프로젝트와 같은 구조로 되어 있음

```sh 
main
  +- java     ---> .java 파일 위치
  +- resource ---> .java 파일 제외한 모든 파일 존재
```

- Springboot Structuring에서 중요한 것은 메인 Application.java의 위치
- Auto Scaning을 위해 보통 java 하위 default 패키지 아래에 위치함

```sh 
src
 +- main
    +- java 
       +- com
          +- example
             +- myapplication  -----> default 패키지 
                 +- Application.java ----> 메인 application 클래스 위치 
                 |
                 +- customer
                 |   +- Customer.java
                 +- order
                     +- Order.java
```
