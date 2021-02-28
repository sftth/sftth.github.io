---
title: "Spring5msa - chap7 스프링 클라우드 활용 MSA 확장"
date: 2020-06-23 00:00:00 +0800
categories: msa
---

### 1. 개요

<br> 7장 주요 내용 <br>

- 스프링 컨피그 서버
- 유레카 서버
- 주울 
- 마이크로서비스 자동 등록 및 서비스 탐색 구현
- 스프링 클라우드 메시징

### 2. 스프링 클라우드 컨피그

- 외부에 있는 환경설정 서버
- 어플리케이션과 서비스의 모든 환경설정 속성 정보를 저장하고 조회하고 관리가 가능하도록 하는 역할 수행

### 3. 환경설정 파라미터 다루는 법

- 프로젝트와 함께 패키징(application.properties, application.yml)

  - 환경 설정 속성을 코드와 분리하고 한곳에 모을 수 있어서 좋음 <br>
  - 하지만 변경되는 환경에 대해 환경 설정 정보가 변경되어야 하고 이는 어플리케이션 전체 빌드를 의미함 <br>

- 프로파일 개념 적용

  - 다른 환경에 대해서는 다른 속성 값들이 적용되게 구분 가능
  - 하지만 여전히 환경설정 정보가 정적으로 어플리케이션과 패키징 된다는 약점

- 환경설정 정보 외부화

  - JNDI 네임스페이스(java:comp/env)를 사용
  - 자바 시스템 환경설정 정보(System.getProperties()) 또는 -D 옵션
  - PropertySource 환경설정
  
    ```java  
    @PropertySource("file:${CONF_DIR}/application.properties")
    public class ApplicationConfig {
    
    }
    ```

  - 자바 어플리케이션 실행 시 환경설정 파일 위치 지정
 
    ```sh  
      java -jar myproject.jar --spring.config.location=<file location>
    ```
 
 - JNDI 연산은 비싸고 유연성이 부족하고 버전 관리도 어려움
 - System.properties는 대규모 배포에 부적절
 - 나머지 방법은 서버에 마운트된 파일 시스템에 의존적
 
 - 그래서 환경설정 정보 관리 솔루션이 필요하다.
 
### 4. 컨피그 서버로 마이크로서비스 구축 
 
- Config Server 와 Actuator 선택
- 원격 깃 기반 환경설정 정보 저장소 또는 로컬 파일 시스템 기반 깃 저장소  사용
- bootstrap.properties 생성
- @EnableConfigServer
- http://localhost:8888/actuator/env
- http://localhost:8888/application/default/master
 
### 5. 클라이언트에서 컨피그 서버 접근

- Config Client 와 Actuator 선택
- bootstrap.properties 생성
- spring.application.name 설정
- spring.cloud.config.uri 설정
- Git 저장소 config-repo 폴더 안에 search-service.properties 파일을 생성
- search-service.properties에 환경설정 정보 추가

### 6. 스프링 클라우드 버스로 환경설정 변경을 전파
 

### 7. 컨피그 서버에 고가용성 적용

- http://localhost:8888/health 접속하여 컨피그 서버 상태 모니터링 가능
