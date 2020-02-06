---
title: "Apache - Proxy 설정 "
date: 2020-01-21 11:00:00 +0800
categories: apache
sidebar:
  nav: "it-sidebar"
---

## 1. Status
보안을 위해 외부망에 있는 Client는 내부망에 있는 Server에 방화벽 설정에 막혀 접속 할 수 없습니다. 
아래 그림과 같이 Client는 방화벽 뒤에 존재하는 Server에 http, ssh 요청을 보내면 방화벽에 막혀 
요청이 목표 서버에 도달하지 않습니다.

![proxy](/assets/images/tomcat/tomcat-proxy001.png)

하지만 필요에 의해 방화벽에 일부 포트를 오픈하여 접근하고자 하는 요구가 있을 수 있습니다. 이 포스트는 
그러한 요구사항을 충족시키기 위해 http는 80 포트, ssh는 29418 포트로 방화벽을 오픈하고 Proxy 서버를 
DMZ 영역에 설정하여 내부망에 있는 목표 서버와 통신을 하는 방법에 관한 것입니다.

![proxy](/assets/images/tomcat/tomcat-proxy002.png)

## 2. Proxy for http

### 2.1 Install Apache
- Proxy로 활용할 apache를 DMZ 영역에 있는 VM에 설치합니다.
- 예제에서 활용한 apache 버전은 2.4_38 입니다.
- apache 설치 예시 이미지

![proxy](/assets/images/tomcat/tomcat-proxy003.png)

- 설명

| 항목 | 설정 값 | 설명 |
| --- | --- | --- |
| 설치 방식 | pakage 방식 | 빌드과정 등 번거롭지만 관리가 판하여 활용 |
| 설치 경로 | /engn001/apache/2.4/ | apache 설치 경로 | 
| 인스턴스 경로 | {설치 경로}/servers/webd-ist-80 | apache 인스턴스 설치 경로 |
| 기동 방법 | {인스턴스 경로}/start.sh 실행 | |
| 정지 방법 | {인스턴스 경로}/stop.sh 실행 | |

{: .notice--warning}
**Note:** 위에 설명한 apache 설치는 한 가지 예시이며 yum 또는 package 어떤 방법이든
apache가 정상 동작하는 설치이면 상관없습니다. apache 설치는 다 할 수 있다는 전제하에
상세히 설명하지 않습니다.
{: .notice--warning} 

### 2.2 http-vhost.conf

- {인스턴스 경로}/conf/extra/httpd-vhost.conf 파일을 아래와 같이 작성함
- 모든 80 포트로 요청은 http://10.1.2.3으로 전달한다는 내용

```sh 
<VirtualHost *:80>
    DocumentRoot "/sorc001/midadm/applications/htdocs"

    <Proxy *>
      Order deny,allow
      Allow from all
    </Proxy>

    ProxyPass / http://10.1.2.3/
</VirtualHost>
```

### 2.3 Test
- Client 콘솔창에서 git clone "http://admin@10.1.2.3/a/test" 수행
- 테스트 결과 성공 이미지

![proxy](/assets/images/tomcat/tomcat-proxy004.png)

## 3 Proxy for ssh
ssh 프록시 처리는 apache를 활용할 수 없고 다른 방법을 활용해야 합니다. 여러 방법이
있겠지만 여기에서는 socat을 활용한 방법을 소개합니다.

### 3.1 Install socat
- Proxy 서버에 socat 설치
- 설치 방법: apt-get install socat 설정

### 3.2 Port forwarding
- socat tcp-listen:29418, reuseaddr, fork tcp:10.1.2.3:29418 &

### 3.3 Test
- Client에서 ssh 호출 시, 성공 확인

![proxy](/assets/images/tomcat/tomcat-proxy005.png)

참고: 
- ssh 프록시 서버 설정: [https://zetawiki.com/wiki/SSH_프록시_서버_설정](https://zetawiki.com/wiki/SSH_프록시_서버_설정)
- socat 설치: [https://zetawiki.com/wiki/우분투_socat_설치](https://zetawiki.com/wiki/우분투_socat_설치)