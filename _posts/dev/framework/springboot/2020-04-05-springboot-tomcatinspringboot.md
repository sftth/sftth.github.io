---
title: "Springboot #2 원리 - 내장 웹 서버"
#excerpt: Springboot 환경에서 Interceptor 설정 하기  
date: 2020-04-05 00:00:00 +0800
last_modified_at: 2020-04-05 00:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: springboot
---

### 1. 내장 톰켓 자동설정 위치

경로: spring-boot-autoconfigure > META-INF <br>

org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration <br>
- ServletWebServerFactoryAutoConfiguration (서블릿 웹 서버 생성)
- TomcatServletWebServerFactoryCustomizer (서버 커스터마이징)

org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration <br>
- 서블릿 작성과 등록 수행

### 2. 내장 웹 서버 응용

#### 2.1 참고 문서 

[Howto embedded web server](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-embedded-web-servers.html)

#### 2.2 톰켓에서 다른 서블릿 컨테이너로 변경

- tomcat dependency 제거

```xml 
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

- 원하는 웹 서버 dependency 추가

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```
- jetty 기동 확인 

```sh 
2020-04-05 18:47:13.206  INFO 6997 --- [           main] o.s.b.web.embedded.jetty.JettyWebServer  : Jetty started on port(s) 8080 (http/1.1) with context path '/'
2020-04-05 18:47:13.210  INFO 6997 --- [           main] c.s.d.s.SpringbootBasicApplication       : Started SpringbootBasicApplication in 2.578 seconds (JVM running for 3.717)
```


### 3. 웹 서버 사용 하지 않기

- application.properties 에 아래 스크립트 등록

```properties
spring.main.web-application-type=none
```
#### 3.1 포트 변경

- application.properties에 아래 스크립트 등록 

```properties
server.port=7070
```

#### 3.2 랜덤 포트 사용 설정

```properties
server.port=0
```

- Random port 확인 하는 Event Listener 구현

```java
@Component
public class PortListener implements ApplicationListener<ServletWebServerInitializedEvent> {
    @Override
    public void onApplicationEvent(ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
        ServletWebServerApplicationContext applicalicationContext =  servletWebServerInitializedEvent.getApplicationContext();
        int port = applicalicationContext.getWebServer().getPort();

        System.out.println("Port: " + port);
    }
}
```

### 4. HTTPS 와 HTTP2

#### 4.1 HTTPS 설정하기

- 참고 문서 [75.8 Configure SSL](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#howto-configure-ssl)

- 키스토어 만들기

```bash
keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore keystore.p12 -validity 4000
```

- 키스토어 생성확인

```sh 
-rw-r--r--   1 Summit  staff   2583 Apr  5 19:20 keystore.p12
```

- application.properties 설정

```properties
server.ssl.key-store=keystore.p12 //또는 server.ssl.key-store=classpath:keystore.p12
server.ssl.key-store-type=PKCS12
server.ssl.key-store-password=123456
server.ssl.key-alias=tomcat
```
- 검증

```sh 
curl -I -k --http2 https://localhost:8080/


HTTP/1.1 200 
Content-Type: text/plain;charset=UTF-8
Content-Length: 12
Date: Sun, 05 Apr 2020 10:34:15 GMT

```

- 기본적으로 springboot에서 http 관련 커넥터가 하나라서 https를 적용하면 http를 사용할 수 없음

- 하지만 아래 방법을 참고하여 코드로 해결할 수 있음

[HTTP 커넥터는 코딩으로 설정하기](https://github.com/spring-projects/spring-boot/tree/v2.0.3.RELEASE/spring-boot-samples/spring-boot-sample-tomcat-multi-connectors) <br>

```java
@SpringBootApplication
public class SpringbootBasicApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootBasicApplication.class, args);
    }


    @Bean
    public ServletWebServerFactory servletContainer() {
        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
        tomcat.addAdditionalTomcatConnectors(createStandardConnector());
        return tomcat;
    }

    private Connector createStandardConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setPort(8080);
        return connector;
    }

}
```
#### 4.2 HTTP2 설정

- HTTP2 설정은 undertow 기반에서 손쉽게 설정 가능함
- 우선 application.properties에 아래 설정을 등록함 

```properties
server.http2.enabled=true
```

- pom.xml에 tomcat을 exclude하고 undertow를 설정 함

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

- Springboot 실행 후 cul 명령어로 확인 결과 HTTP/2 확인

```sh 
sftth-mac:springboot-basic Summit$ curl -I -k --http2 https://localhost:8443/
HTTP/2 200 
content-type: text/plain;charset=UTF-8
content-length: 12
date: Sun, 05 Apr 2020 14:07:59 GMT
```

- 톰켓의 경우 톰켓 9버전대와 java 9+ 버전을 사용하면 별다른 설정없이 HTTP/2 설정 가능
