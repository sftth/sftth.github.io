---
title: "Springboot 기초 - 2부 시작"
date: 2020-01-29 11:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

Springboot 소개


**Info Notice:** 이 포스트는 Inflearn 온라인 학습 "스프링 부트 개념과 활용"에서 참고할 사항을 도출하여 빠르게 참고하고자 정리한 블로그입니다.
{: .notice--info}

## 1. Springboot 소개

- Springboot Reference site

[Springboot 2.0.3.RELEASE doc](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#getting-started-introducing-spring-boot)

## 2. Springboot 시작

   - Reference Doc 경로
    
   [Spring.io](https://spring.io) > Projects > Springboot > LEARN > Reference Doc
   
   - 프로젝트 만들기
   
   [10.1.1 Maven Installation](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/#getting-started-maven-installation) 참고
   또는 Intellij의 경우, Spring Initializer / Maven / Gradle 활용. <br>  
   
   - New Project
   
   GroupId, ArtifactId, Version 입력
   
   | 항목     | 값          |
   | ---     | ---        |
   | GroupId | com.summit |
   | ArtifactId | whms    |
   | Version | 1.0.0      |
   
   - pom.xml 기본 설정
   
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
   - Application class 생성
   
   ```java 
   @SpringBootApplication
   public class WhmsApplication{
       public static void main(String[] args) {
              springApplication.run(WhmsApplication.class, args);
       }
   }
   ```
## 3. Springboot 프로젝트 구조

- Maven(또는 Gradle) 프로젝트 구조와 동일

|  항목          |   값               |
| ---           | ---               | 
| Source        | src/main/java     |
| Resource      | src/main/resource |
| Test Source   | src/test/java     |
| Test Resource | src/test/resource |

- Main Application 위치

기본 package 바로 아래에 위치(component scan 때문)
   