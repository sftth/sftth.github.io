---
title: "Springboot 구현 - 01.환경 구성"
date: 2020-02-05 00:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---
springboot 구현에 대한 이해를 돕고자 springboot 기반 업무관리 시스템을 만들고자 합니다. 이 포스팅은 시스템 구현을 위한 환경 구성에 관한 것입니다.

## 1.Local Environment
개발을 위한 환경 정보는 다음과 같습니다.

### 1.1 환경 정보

| 항목    | 값   | 설명 |
| ---     | --- | --- |
| IDE     |IntelliJ IDEA Ultimate 2019.3 | springboot 개발을 위한 개발도구       |
| Runtime | Java 1.8.0_231               | springboot 구현을 위한 java 버전 정보 |
| Redis   | Redis 5.0.7 64bit            | Session Clustering을 위한 Redis 정보  |

## 2.Generating Project Process
### 2.1 Step1
- IntelliJ 최상단 [FILE] > [New] > [Project...] 선택 <br>
![whms](/assets/images/springboot/springboot-whms001.png)

- Spring Initializr > 속성 설정 후 [Next] 선택 <br>
![whms](/assets/images/springboot/springboot-whms002.png)
   
- 속성 설정 정보

   | 항목 | 값 | 설명 |
   | --- | --- | --- |
   | Project SDK | 1.8.0_231 | Java 버전 선택 |
   | Choose Initializr Service URL| Default(https://start.spring.io) | 초기화 서비스는 기본값 유지|

### 2.2 Step2
- 프로젝트 생성을 위한 Metadata 입력 > [Next] 선택
![whms](/assets/images/springboot/springboot-whms003.png)
   
- Metadata 정보
  
   | 항목 | 입력 값 | 설명 |  
   | ---  | --- | --- |
   | Group    | com.***** | 최상위 패키지 명 |
   | Artifact | whms | 패키징 아티팩트 명 |
   | Type     | Maven Project | 프로젝트 유형 |
   | Language | Java | 플랫폼 언어 |
   | Packaging| Jar | 패키징 포맷 |
   | Java Version | 8 | 자바 버전 정보 |
   | Version | 0.0.1-SNAPSHOT | 패키지 버전 정보
   | Name | whms | 프로젝트 명 | 
   | Description | 사용자 정의 | 프로젝트 설명 정보 |
   | Package | 자동완성 | 위 정보 바탕으로 자동 생성 |

### 2.3 Step3
개발에 필요한 라이브러리에 대해 Dependencies 설정합니다. <br>

- lombok 설정 <br>
![whms](/assets/images/springboot/springboot-whms005.png)

- web 설정 <br>
![whms](/assets/images/springboot/springboot-whms006.png)    

- 설정 정보
   
   | 항목 | 설정값 | 설명 |
   | --- | --- | --- |
   | Developer Tools | Lombok | Model Class getter, setter 생성 편의성 제공용(차후 예제로 확인) |
   | Web             | Spring Web | webMVC 구현을 위함 |
   |                 | Session Clustering| session clustering을 위함 |
   
- 프로젝트 명 > 프로젝트 위치 설정 <br>
![whms](/assets/images/springboot/springboot-whms007.png)
   
## 3.Final Status
아래 이미지와 같이 프로젝트가 생성되면 작업은 최종 완료됩니다. <br>
- 최종 완료 이미지 <br>
![whms](/assets/images/springboot/springboot-whms004.png)