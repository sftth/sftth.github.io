---
title: "AWS Autoscaling - CICD를 위한 Jenkins구성"
date: 2020-01-29 11:00:00 +0800
categories: cloud
sidebar:
  nav: "it-sidebar"
---

Autoscale 환경의 인스턴스에 최신 소스를 반영하는 문제는 고민거리이다. 인스턴스는 동적으로 생성/소멸되기 때문에 소스 반영을 위한 고정된 목적지를 알 수가 없기 때문이다. 이 포스팅은 Jenkins을 활용하여 해당 문제 해결 방법을 제시하고자 한다. 

## Plugin
먼저, Jenkins과 S3 연동을 위한 플러그인을 설치

### 1. Jenkins > Jenkins 관리 > 플러그인 관리

   ![Jenkins](/assets/images/autoscaling-jenkins001.png)

### 2. S3 publisher plugin 설치

   ![Jenkins](/assets/images/autoscaling-jenkins002.png)


## Job
빌드 후 생성 된 war를 S3에 업로드하기 위한 Job생성

### 1. 새로운Item > Maven project

   ![Jenkins](/assets/images/autoscaling-jenkins003.png)
   
### 2. 소스 코드 관리 > Git
- 코드를 관리하는 형상관리서버는 Git으로 가정
- 아래와 같이 Git URL 정보와 Branch 정보를 입력함.
  ![Jenkins](/assets/images/autoscaling-jenkins004.png)
    
  | 항목              | 값                             |
  | ----------------- | ------------------------------ |
  | Repository URL    | https://${github DNS}/${repository name}.git |
  | Branches to build | */master               |

### 3. Build
- 소스 빌드는 Maven 기준으로 수행
- 아래 정보를 Build 항목에 입력

  | 항목 | 설명 | 값 | 
  | ---- | --- | --- |
  | Root POM    | pom 파일 명 | pom.xml |
  | Goals and options | pom 옵션 | clean install package |

### 4. 빌드 후 조치
- 빌드 후 조치 항목 중 Publish artifacts to S3 Bucket을 선택
- 표에 입력된 정보를 아래 이미지와 같이 각 항목에 입력
- 표에 표시 되지 않는 항목은 Default 유지
  ![Jenkins](/assets/images/autoscaling-jenkins005.png)

  | 항목 |     | 설명 | 값 |
  | --- | --- | --- | --- |
  | S3 profile | | Jenkins global config 설정된 S3 관련 프로파일 명* | ${프로파일 명}|
  | Files to upload | Source | 배포 war 경로 |${war} 경로 |
  | | Exclude | 업로드 제외 요소 설정 | 설정 없음 |
  | | Destination bucket | S3에 설정한 버킷 명| ${버킷 명}|
  | | Storage class| S3 버킷 클래스 속성| STANDARD | 
  | | Bucket Region| 버킷 생성 리전| ${버킷 생성 리전} |
  
  *${} 형태는 설정자가 프로젝트 표준에 맞춰 입력하는 영역을 의미 함. <br>
- 참고
  - S3 profile 정보는 Jenkins 관리 > 시스템 설정 메뉴에서 아래 이미지 참조하여 수행 함
![Jenkins](/assets/images/autoscaling-jenkins006.png) 
  - 설정 내용의 핵심은 Jenkins이 있는 EC2의 IAM Role을 통해 S3 관련 작업을 할 것을 명시
  - 위 사항은 이미 Jenkins가 설치된 EC2는 S3 접근 권한을 가진 사용자의 IAM 설정이 완료 된 것으로 가정 함.

