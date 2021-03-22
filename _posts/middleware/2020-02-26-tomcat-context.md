---

title: "Tomcat 서버설정- Context 두 개 설정"
#excerpt: AWS Autoscale 기본 설정  
date: 2020-02-26 11:00:00 +0800
last_modified_at: 2020-02-26 11:00:00 +0800
header:
  teaser: /assets/images/01_teaser/cat-brown.png
  overlay_image: /assets/images/01_teaser/cat-brown.png
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/cat-brown.png
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: tomcat
---
## 1. 개요
- 톰켓 단일 인스턴스에 두 개의 다른 소스를 소스 디렉토리에 위치시키고 context 설정을 통해
다른 URI로 접근하고자 함

![context](/assets/images/tomcat/tomcat-context001.png)

## 2. 설정
- server.xml에 context 설정을 두 개 설정

```xml
<Host name="localhost" unpackWARs="true" autoDeploy="false">
    <Context path="/source1" docBase="/sorc001/appadm/applications/source1.war" reloadable="false" privileged="true"/>
    <Context path="/source2" docBase="/sorc001/appadm/applications/source2.war" reloadable="false" privileged="true"/>
... 중략
</Host>
```

## 3. 에러 대응

- server.xml에 context 설정을 두 개 설정시 아래 에러 발생하면서 톰켓 기동 실패하는 경우

- 에러 메시지
> java.lang.illegalargumentexception: 이름이 spring_web 인 둘 이상의 fragment들이 발견되었습니다

- 조치 방법: 프로젝트 WEB-INF 하위에 있는 web.xml에 다음과 같이 설정

```xml 
...중략...
<display-name>devon-sample-simpleweb</display-name>
<absolute-ordering />
... 중략 ...
```
