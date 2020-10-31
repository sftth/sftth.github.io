---
title: "Springboot 구현 - 02.Redis 설치"
#excerpt: Springboot 환경에서 Interceptor 설정 하기  
date: 2020-02-05 00:00:00 +0800
last_modified_at: 2020-02-05 00:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: springboot
---
이 포스팅은 springboot 구현시 session 공유를 위해 Redis를 활용하는 방법 중, 설치에 관한 내용입니다.

## 1. Redis on Window
### 1.1 Download
- https://github.com/microsoftarchive/redis/releases 에서 stable 버전 설치 <br>

![whms](/assets/images/springboot/springboot-whms113.png)

### 1.2 Run  
- Window + R > 서비스 > 검색 > Redis 실행 확인 <br>

![whms](/assets/images/springboot/springboot-whms112.png)

- ping 테스트

```sh
C:\> C:\Program Files\Redis>redis-cli.exe -a passwd
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> poing
(error) ERR unknown command 'poing'
127.0.0.1:6379>
```

## 2. Redis on Linux
### 2.1 Redis Install
- 종속 라이브러리 설치

```sh
sudo yum -y install gcc-c++
```
- Redis 설치 파일 다운로드 및 설치

```sh
[ec2-user@ip-**** ~]$ wget http://download.redis.io/releases/redis-5.0.7.tar.gz
[ec2-user@ip-**** ~]$ tar xzf redis-5.0.7.tar.gz
[ec2-user@ip-**** ~]$ cd redis-5.0.7
[ec2-user@ip-**** redis-5.0.7]$ make
```

{: .notice--warning}
**Note:** make 명령어 수행 시, jemalloc/jemalloc.h no such file or directory redis 발생하는 경우 <br>
[ec2-user@ip- redis-5.0.7]$ make distclean <br>   
[ec2-user@ip- redis-5.0.7]$ make <br>
수행하여 해결
{: .notice--warning}   

### 2.2 Redis Run
- 기동 <br>
아래 명령어를 통해 Redis를 기동합니다.

```sh
[ec2-user@ip- redis-5.0.7]$ ./src/redis-server
```
- 기동 로고 확인<br>
기동이 정상적으로 수행되면 아래와 같은 메시지를 확인할 수 있습니다.

```sh
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 5.0.7 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 31479
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'
```

{: .notice--info}
**ProTip:** 위 방식으로 기동하면 별도의 설정 정보 없이 redis가 foreground형태로 기동하는데
그렇게 되면 콘솔화면에서 다른 작업을 수행할 수 없습니다. 그래서 보통 redis 설정 파일 적용하여 
background 기동하도록 설정합니다. <br>
설정 적용및 background 기동방법은 아래 명령어와 같습니다. <br><br>
**nohup ./src/redis-server ./redis.conf &**
{: .notice--info}

- Password 설정 <br>
Redis의 경우 기본적으로 원격접속 시 보안을 위해 Password를 통한 접속만 허용합니다. 따라서 
redis.conf 파일에서 다음 설정을 통해 Password 접속 기능을 활성화 합니다.<br>

![whms](/assets/images/springboot/springboot-whms101.png)

### 2.3 Cli Start
Redis에 접속하여 key값 확인등 수행할 수 있는 cli 접속 방법은 아래 명령어를 통해 수행합니다.<br>
```sh
[ec2-user@ip- redis-5.0.7]$ ./src/redis-cli -a passwd $ -a 옵션은 redis.conf에 설정한 패스워드를 입력
```

### 2.4 redis Stop
기동 중인 Redis 정지는 cli를 통해 가능하고 방법은 아래 명령어와 같습니다.
```sh
./src/redis-cli -a passwd
> shutdown
```

## 3. Redis Desktop Manager
Window환경에서는 Redis 관리를 위한 접속 도구를 활용할 수 있습니다. 설치 및 활용 방법은 아래와 같습니다.
### 3.1 Download
- 다운로드 사이트: [https://redisdesktop.com/](https://redisdesktop.com/) <br>

![whms](/assets/images/springboot/springboot-whms102.png)

- Download > Personal 14일 무료 버전 <br>

![whms](/assets/images/springboot/springboot-whms103.png)

- Sign up

![whms](/assets/images/springboot/springboot-whms104.png)

- 입력한 이메일 확인 및 Confirm 수행

![whms](/assets/images/springboot/springboot-whms105.png)

- redis-desktop-manager-2.109.5.176.exe 다운로드

![whms](/assets/images/springboot/springboot-whms106.png)

### 3.2 Install

- Redis Desktop Manager 설치

![whms](/assets/images/springboot/springboot-whms107.png)

### 3.3 Run

- 프로그램 실행 > 계정 정보 입력

![whms](/assets/images/springboot/springboot-whms108.png)

- 프로그램 기동 확인 > Connect to Redis Server 클릭

![whms](/assets/images/springboot/springboot-whms109.png)

- 접속 정보 입력 > [Test Connection] 클릭하여 접속 상태 확인

![whms](/assets/images/springboot/springboot-whms110.png)

- 최종 접속 확인

![whms](/assets/images/springboot/springboot-whms111.png)

## 4. Springboot Configuration
설치된 Redis와 연동을 위해 Springboot에도 설정이 필요합니다. 
다음은 Springboot 설정 프로세스에 대한 설명입니다.

### 4.1 maven configuration
- springboot에서 redis 연동을 위해 필요한 dependency 설정을 pom.xml에 설정

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.session</groupId>
  <artifactId>spring-session-data-redis</artifactId>
</dependency>
```
### 4.2 application property

- application.properties 파일에 Redis 연동을 위한 속성 정보를 등록

```xml
spring.redis.port=6379
spring.redis.host=*****
spring.redis.password=*****
```

### 4.3 sample source

- Redis 연동 테스트를 위한 Controller를 구현
- sample code

```java
@RestController
public class SessionController {

    @GetMapping("/hi")
    public Map<String, String> getSession(HttpSession session) {
        UUID uid = Optional.ofNullable((UUID) session.getAttribute("uid")).orElse(UUID.randomUUID());
        session.setAttribute("uid", uid);

        Map<String, String> m = new HashMap<>();
        m.put("instance_ip", "127.0.0.1");
        m.put("uuid", uid.toString());

        return m;
    }
}
```

## 5. Result
Controller 호출시 Redis에 등록된 Session 정보를 화면과 manager를 통해 확인 할 수 있습니다.

- Controller 호출 결과

![whms](/assets/images/springboot/springboot-whms114.png)

- Redis Desktop Manager 확인 결과

![whms](/assets/images/springboot/springboot-whms115.png)
