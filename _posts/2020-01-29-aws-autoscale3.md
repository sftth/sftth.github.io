---
title: "AWS Autoscaling - AMI 내부 구성"
date: 2020-01-29 11:00:00 +0800
categories: cloud
sidebar:
  nav: "it-sidebar"
---

Autoscale 환경에서 동적으로 기동하는 인스턴스는 시시각각 변화하는 소스의 최신 버전을 능동적으로 갱신하여 기동할 수 있어야 한다. 이를 위해 Autoscale에 사용할 AMI를 생성시, AMI의 대상이 되는 초기 인스턴스에 몇가지 설정이 필요하다. 이 포스트는 Autoscale 환경에서 능동적으로 소스를 갱신할 수 있는 AMI 구성 방법을 설명한다.

## AWS CLI
- Jenkins 서버에 AWS Cli 설치 <br>
  ```sh
  $ > sudo yum install awscli
  ```

## S3 접근 권한
- EC2에 S3 접근 권한 부여에 사용할 사용자 생성
- IAM > 사용자 > 사용자 추가 <br>
![IAM](/assets/images/autoscaling301.png)
- 사용자 세부 정보 설정

  | 항목 | 값 |
  | --- | --- |
  | 사용자 이름 | ${사용자 이름} |
  | 액세스 유형 | 프로그래밍 방식 엑세스 |

- 권한 설정 <br>
![IAM](/assets/images/autoscaling302.png)

- 그룹 생성 <br>
![IAM](/assets/images/autoscaling303.png)

- 정책 필터 > S3FullAccess > 그룹생성
![IAM](/assets/images/autoscaling304.png)

- 태그 > 검토 > 사용자 생성

- EC2에서 사용자 Access Key, Secret Access Key 등록
  ```sh
  $ > aws configure

  AWS Access Key ID: 
  AWS Secret Access Key:
  Default region name: 
  Default output format:
  ```
## 스크립트
Autoscale의 대상이 WAS 서버이고 이를 AMI로 만든다는 가정 하에 Tomcat start 스크립트에 S3에 있는 war 파일을 가져오는 스크립트를 추가함.

```sh
echo "1. delete artifact"
rm -rf /sorc001/appadm/application/mainWebApp.war
echo "2. artifact is deleted"

echo "3. sync s3"
aws s3 sync s3://sftth322-autoscale-artifact/target/ /sorc001/appadm/application
sleep 5
echo "4. sync s3 finished"
echo "5. start tomcat"

${CATALINA_HOME}/bin/startup.sh
```