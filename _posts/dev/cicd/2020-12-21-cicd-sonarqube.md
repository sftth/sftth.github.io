---
title: Jenkinsfile에 SonarQube 연동하기 
excerpt: Jenkins pipeline에 SonarQube 연동 설정하기 
date: 2020-12-21 00:00:00 +0800
last_modified_at: 2020-12-21 00:00:00 +0800
categories: cicd
header:
  teaser: https://live.staticflickr.com/1872/44696258862_8135bd2fdf_k.jpg
  overlay_image: https://live.staticflickr.com/1872/44696258862_8135bd2fdf_k.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/1872/44696258862_8135bd2fdf_k.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

# SonarQube 실행

- docker 기반 sonarqube를 기동함 
- 재활용을 위해 docker-compose yml 파일로 작성 함 
- 운영 수준의 동작을 위해 데이터 저장용 DB를 postgresql로 함  
- 영속적인 데이터 저장을 위해 volume mount 구성

## docker-compose-sonarqube.yml 내용 

<details><summary>yml 내용</summary>
<div markdown="1">
    
```sh

version: "3.1"

services:
  sonarqube:
    container_name: summit-sonarqube
    image: sonarqube:8-community
    ports:
      - 9000:9000
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - ~/mount/sonarqube/sonarqube_data:/opt/sonarqube/data
      - ~/mount/sonarqube/sonarqube_extensions:/opt/sonarqube/extensions
      - ~/mount/sonarqube/sonarqube_logs:/opt/sonarqube/logs
      - ~/mount/sonarqube/sonarqube_temp:/opt/sonarqube/temp
    ports:
      - "9000:9000"
  db:
    container_name: summit-sonarqube-db
    image: postgres:12
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - ~/mount/sonarqube/postgresql:/var/lib/postgresql
      - ~/mount/sonarqube/postgresql_data:/var/lib/postgresql/data
```
        
</div>
</details>

## sonarqube 기동 

- docker compose yml을 활용하여 기동 함 

```sh 
$ docker-compose -f docker-compose-sonarqube.yml up -d 
```

***

# Token 발급

- jenkins 연동을 위한  sornarqube token을 발급 

## Token 발급 절차

1. sonarqube 로그인(admin/admin)
2. [My Account] > [Security] > "Generate Tokens" > 임의 문자 입력 > [Generate] > 토큰 발급

<details><summary>관련 이미지</summary>
<div markdown="1">

<p align="center">
    <img src="/assets/images/cicd/sonarqube001.png" width="600" height="400" alt="sonarqube">
</p>

</div>
</details>

## Jenkins에 설정

1. Jenkins 관리 > 플러그인 관리 > SonarQube Scanner tjfcl
2. Jenkins 관리 > 시스템 설정 > SonarQube Server > [Add SonarQube] > 정보 설정 
3. Server authentication token kind, secret, ID 설정 > secret 에는 sonarqube token을 설정 > [ADD] 

<details><summary>관련 이미지</summary>
<div markdown="1">

<p align="center">
    <img src="/assets/images/cicd/sonarqube002.png" width="600" height="400" alt="sonarqube">
    <img src="/assets/images/cicd/sonarqube003.png" width="600" height="400" alt="sonarqube">
</p>

</div>
</details>

***

# Jenkinsfile 수정 

- jenkins - sonarqube 연동에 따라 pipeline 수행을 위한 stage 추가 

<details><summary>jenkinsfile 내용</summary>
<div markdown="1">
  
```yml 
pipeline {
	agent any

	stages {
    <중략>
    ...
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh './mvnw sonar:sonar'
                }
            }
        }
    }
    <중략>
    ...
}
```
        
</div>
</details>

***

# Sonarqube Quality Gates webhooks 설정

## Quality Gates 조건 설정 

1. sonarqube > Quality Gates > [Create] > [Add Condition]
2. 조건 입력

| 조건 | 입력 값 |
| --- | ---   |
| On Overall Code | Quality Gate fails when : Bugs |
| Operator | is greater than 0 |

3. [Add Condition] 선택 

## Quality Gates webhook 설정 

1. [Administration] > [Configuration] > [Webhooks]
2. [Create] > Name, URL: http://jenkins 도메인:6060/sonarqube-webhook/ 설정

## jenkinsfile 수정

<details><summary>jenkinsfile 내용</summary>
<div markdown="1">
  
```yml 
pipeline {
	agent any

	stages {
    <중략>
    ...
        stage('SonarQube Quality Gate') {
            steps {
                timeout(time:30, unit:'MINUTES') {
                    waitForQualityGate abortPipeline:true
                }
            }
        }
    <중략>
    ...
}

```
        
</div>
</details>


