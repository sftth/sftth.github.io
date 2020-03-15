---
title: "Docker - 11. Jenkins Dockerfile 만들기"
date: 2020-03-13 00:00:00 +0800
categories: docker
sidebar:
  title: "Development"
  nav: "dev-sidebar"
---
# 개요

AWS EC2에서 구동 중인 Jenkins을 Docker image로 만들기 위해 Dockerfile을 제작하는 과정을 포스팅 합니다.


## 1. EC2 기동

먼저, Jenkins이 설치되어 있는 AWS EC2를 기동 합니다. <br>

![docker-basic011](/assets/images/docker/docker-basic1101.png)

기동된 EC2에 콘솔로 접속합니다. <br>

```sh 
ssh -i *****-keypair.pem ec2-user@192.168.*.*
```

## 2. Jenkins 정보 확인

Jenkins 설치 경로를 확인 합니다. <br>
Jenkins은 일단 /sorc001/appadm/ciserv 라는 경로에 설치되어 있습니다. <br>

```sh 
[ec2-user@ip-*-*-*-* ciserv]$ cd /sorc001/appadm/ciserv/
[ec2-user@ip-*-*-*-* ciserv]$ ll
total 8
drwxr-xr-x  6 ec2-user ec2-user  235 May  2  2019 ant
drwxr-xr-x  2 ec2-user ec2-user   54 Feb 12 01:53 bin
drwxr-xr-x 11 ec2-user ec2-user  149 May  9  2019 csvn
drwxrwxr-x  5 ec2-user ec2-user   45 Sep  3  2019 git
drwxr-xr-x  6 ec2-user ec2-user  112 Feb  1  1980 gradle
drwxr-xr-x  7 ec2-user ec2-user  245 Jul  4  2019 java8
drwxr-x--- 15 ec2-user ec2-user 4096 Mar 13 07:54 jenkins
drwxr-xr-x  3 ec2-user ec2-user   19 Sep  3  2019 lib64
drwxrwxr-x  6 ec2-user ec2-user   99 Sep  2  2019 maven
drwxrwxr-x 26 ec2-user ec2-user 4096 Jan 10 04:28 repository
drwxr-xr-x  4 ec2-user ec2-user   30 Sep  3  2019 share
drwxrwxr-x  9 ec2-user ec2-user  220 Sep  2  2019 tomcat9

```

현재 Jenkins은 EC2 VM 기동시 자동 실행되도록 설정해 두었습니다. <br>
Docker image 작업 후 결과 비교를 위해 Jenkins을 브라우저로 접속하여 이미 생성된 Jenkins Job의 상태를 확인 하겠습니다. <br>

![docker-basic011](/assets/images/docker/docker-basic1102.png)

몇 개의 Job이 이미 생성이 되어 있음을 확인 할 수 있습니다. <br>

## 3. Dockerfile 만들기

```dockerfile

FROM centos:7

RUN ["/bin/bash","-c","mkdir -p /sorc001/appadm"]

ADD https://********.amazonaws.com/ciserv.tar.gz  /sorc001/appadm

RUN ["/bin/bash","-c","groupadd -r ec2-user && useradd -m -g ec2-user ec2-user"]

RUN ["/bin/bash","-c","chown -R ec2-user:ec2-user /sorc001"]

USER ec2-user

WORKDIR /sorc001/appadm

RUN ["/bin/bash","-c","tar zxf ./ciserv.tar.gz"]

RUN ["/bin/bash","-c","rm -rf ./ciserv.tar.gz"]

CMD /sorc001/appadm/ciserv/bin/start.sh -DFOREGROUND

```

## 참고 s3 public 설정

1.4GB나 되는 파일을 컨테이너에 집어 넣을 방법이 없는 관계로 s3에 넣고 s3 버킷을 public 설정한 다음 다운로드 하기로 하겠습니다.<br>
aws s3 public 설정은 권장하는 사항은 아니지만 필요시 사용 후 다시 퍼블릭 접근 해제를 하는 것으로 관리하는 것으로 하겠습니다.<br>

- s3에 퍼블릭 설정하고자 하는 버킷 선택하고 [퍼블릭 액세스 설정 편집]을 클릭합니다. 

![docker-basic11](/assets/images/docker/docker-basic1103.png)

- 아래 이미지 처럼 설정 후 [저장] 합니다.

![docker-basic11](/assets/images/docker/docker-basic1104.png)

- 퍼블릭 액세스 설정이 된 버킷 내부에 퍼블릭 접근을 허용하고자 하는 파일을 클릭 한 후 아래 이미지와 같 [퍼블릭으로 설정]을 하면
비로소 s3에 원하는 파일을 권한 체크없이 다운로드 할 수 있게 됩니다.

![docker-basic11](/assets/images/docker/docker-basic1105.png)

## Dockerfile 빌드

작성한 dockerfile은 github에 커밋하고 dockerhub와 연동하여 빌드합니다. <br>

**알림** github와 dockerhub를 이용한 dockerfile 빌드에 관한 상세 방법은 차후에 포스팅하고 링크를 걸어두겠습니다. 
그 때면 이 알림은 없어져 있겠죠. 
{: .notice}

- 아래는 dockerhub를 이용한 빌드 화면입니다.

![docker-basic11](/assets/images/docker/docker-basic1106.png)

## docker run

- dockerhub에서 빌드가 완료된 이미지를 활용하여 컨테이너를 기동 시킵니다.

```sh 
[ec2-user@*** jenkins]$ docker run -it -d -p 6060:6060 --name jacobs sftth/jacobs:1.0

```

## 결과 확인

- 컨테이너화 이전에 사용했던 것과 같은 구성의 jenkins을 볼 수 있습니다.

![docker-basic011](/assets/images/docker/docker-basic1102.png)

사실 이 번 dockerfile 작성 과정 속에서 여러 시행 착오가 있었으나 막상 포스팅 하니깐 그런 시행착오는 한 개도 
쓰지 못해서 아쉬움이 듭니다. 대신 나중에 활용 할 지도 모르니 아래 참고로 링크 걸어 봅니다. <br>

[cli를 통한 aws s3 접근](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-services-s3-commands.html) <br>
[sudo 안 쓰고 docker 사용](https://www.slipp.net/questions/485) <br>
[리눅스 사용자 추가, 삭제](https://withcoding.com/101) <br>
csvn httpd bad user name 발생하면 csvn>data>conf>csvn-main.....conf를 확인 할 것 