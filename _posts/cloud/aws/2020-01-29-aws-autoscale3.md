---
title: AWS Autoscale
excerpt: AMI 내부 구성  
date: 2020-01-29 11:00:00 +0800
last_modified_at: 2020-01-21 11:00:00 +0800
header:
  teaser: /assets/images/01_teaser/clouds-gray.jpg
  overlay_image: /assets/images/01_teaser/clouds-gray.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/clouds-gray.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: aws
---

AWS Autoscale 환경에서 동적으로 기동하는 인스턴스는 소스의 최신 버전을 능동적으로 갱신하여 기동할 수 있어야 합니다. 
이를 위해 AWS Autoscale에 사용 할 AMI를 만들기 전에 몇 가지 설정이 필요합니다. <br>

이 포스트는 AWS Autoscale 환경에서 능동적으로 소스를 갱신할 수 있는 AMI 구성 방법을 설명합니다.

### 1. AWS CLI 설치

tomcat이 설치된 서버에 AWS Cli를 설치 합니다. <br>

```sh
$ > sudo yum install awscli
```

### 2. S3 접근 권한 사용자 생성

tomcat이 설치된 서버는 AWS S3로 부터 최신 소스를 다운로드 하고자 합니다. 
이를 위해 먼저, S3 접근 권한 부여에 사용할 사용자를 생성합니다. <br>

- IAM > 사용자 > [사용자 추가]

![IAM](/assets/images/autoscaling301.png)

- 사용자 세부 정보 설정

![IAM](/assets/images/autoscaling302.png)

- 권한 설정 > 그룹 생성

![IAM](/assets/images/autoscaling303.png)

- 정책 필터 > S3FullAccess > 그룹생성

![IAM](/assets/images/autoscaling304.png)

- 태그 > 검토 

![IAM](/assets/images/autoscaling305.png)

- 사용자 생성 완료

![IAM](/assets/images/autoscaling306.png)

- 생성된 사용자 .csv 파일 다운로드

![IAM](/assets/images/autoscaling307.png)

**경고:** 사용자 .csv 파일은 반드시 안전하게 보관해야 합니다. 분실 또는 유출시 사용할 수 없거나
악의적인 사용으로 피해를 볼 수 있습니다.
{: .notice--warning}

### 3. EC2에 aws configure 설정

tomcat이 설치된 서버에서 aws cli로 s3에 접근하기 위해 위에 생성된 사용자의 Access Key, Secret Access Key를 등록합니다.
등록 방법은 ***aws configure*** 명령어를 통해 수행 됩니다.

```sh
$ > aws configure

AWS Access Key ID: 
AWS Secret Access Key:
Default region name: 
Default output format:
```

### 4. 스크립트 추가

Autoscale의 대상이 WAS 서버이고 이를 AMI로 만든다는 가정 하에 Tomcat start 스크립트에 S3에 있는 war 파일을 가져오는 스크립트를 추가합니다.

```sh
echo "1. delete artifact"
rm -rf /sorc001/appadm/application/mainWebApp.war
echo "2. artifact is deleted"

echo "3. sync s3"
aws s3 sync s3://XXXXXXX-artifact/target/ /sorc001/appadm/application
sleep 5
echo "4. sync s3 finished"
echo "5. start tomcat"

${CATALINA_HOME}/bin/startup.sh
```