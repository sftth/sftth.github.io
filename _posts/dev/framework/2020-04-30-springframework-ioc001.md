---
title: "SpringFramework Core - IoC"
date: 2020-04-30 00:00:00 +0800
categories: 
 - springframework
tags:
 - ioc
sidebar:
  nav: "dev-sidebar"
---

### 1부 스프링 IoC 컨테이너와 빈 

IoC(Inversion of Control)은 사용될 객체를 사용할 객체에서 직접 만드는 것이 아닌 제 3의 담당자에게 주입을 받는 방법 <br>

#### 1. 스프링 IoC 컨테이너 
- BeanFactory
- 애플리케이션 컴포넌트의 중앙 저장소
- 빈 설정 소스로 부터 빈 정의를 읽고 빈을 구성하고 제공

#### 2. Bean
- 스프링 IoC 컨테이너가 관리하는 객체 

#### 3. ApplicationContext
- 참고 [API DOC](https://docs.spring.io/spring/docs/5.2.6.RELEASE/javadoc-api/)
- BeanForctory
- 메시지 소스 처리 기능(i18n) 
- 이벤트 발생 기능
- 리소스 로딩 기능

### 2부 ApplicationContext와 다양한 빈 설정 방법


### 3부 @Autowire

### 4부 @Component와 컴포넌트 스캔

#### 1. 컴포넌트 스캔 주요기능

- 스캔 위치 설정(default package 라든지..)
- 필터: 어떤 어노테이션을 스캔 할지 또는 하지 않을지

#### 2. 스캔 대상 어노테이션

- @Repository
- @Service
- @Controller
- @Configuration

#### 3. 동작 원리

- @ComponentScan은 스캔할 패키지와 어노테이션에 대한 정보
- 실제 스캐닝은 ConfigurationClassPostProcessor라는 BeanFactoryPostProcessor에 의해 처리

### 5부 빈의 scope

