---
title: "Springboot - Data"
excerpt: Springboot에서 Mysql 접속하기 
date: 2020-11-12 00:00:00 +0800
last_modified_at: 2020-11-12 00:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: springboot
---
## Mysql 기동 및 접속 
### 1. mysql docker-compose 파일 작성

#### 1.1 docker-compose
```yaml
version: '3.1'
services:
  whms-mysql:
    container_name: whms-mysql
    image: mysql:latest
    ports:
      - 3306:3306
    volumes:
      - /Users/summit/mount/mysql/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: "!qusrud82"
      MYSQL_DATABASE: "whms"
      MYSQL_USER: "jacob"
      MYSQL_PASWORD: "!qusrud82"
```

#### 1.2 기동
```sh
$ docker-compose -f docker-compose-mysql.yaml up -d
```

### 2. connection

#### 2.1 문제
"public key retrieval is not allowed"
#### 2.2 원인
mysql 8.x 버전 이후로 발생
#### 2.3 해결
Debeaver > Connecton Setings > Driver properties > 
allowPublicKeyRetrieval > false에서 true로 변경

### 3. access denied {user}

#### 3.1 문제 
- debeaver에서 Mysql접속 시 아래 문제 발생 
- "Access denied for user 'jacob@172.30.0.1'(using password:YES)"

#### 3.2 해결 
```sh
CREATE USER 'jacob'@'%' IDENTIFIED BY '!qusrud82';
GRANT ALL PRIVILEGES ON whms.* to 'jacob'@'%' with grant option;
flush privileges; 
```

### 4. table 만들기
```sh
create table users (
name char(20),
email char(50),
password(20),
primary key(email)
)engin=InnoDB default cahrset=utf8;
```

## SpringBoot jdbc 설정

### 5. dependency 설정

```xml 
<!-- spring jdbc 용 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<!-- MySql 접속 용 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

### 6. application.properties 설정 
```xml 
#Database
spring.datasource.url=jdbc:mysql://localhost:3306/whms?useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=jacob
spring.datasource.password=*****
```

### 7. 검증 
Runner Class 구현 및 기동 

```java 
@Component
public class WhmsRunner implements ApplicationRunner {
    @Autowired
    DataSource dataSource;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        try(Connection connection = dataSource.getConnection()) {
            System.out.println("@@@@"+ dataSource.getConnection());
        }
    }
}
```
Log 결과

```bash 
@@@@HikariProxyConnection@104298781 wrapping com.mysql.cj.jdbc.ConnectionImpl@3b0c3951
```

## DBCP

### 8. 지원가능 DBCP

- HikariCP
- TomcatCP
- Commons DBCP2

**Info Notice:** 
 SpringBoot 공식 문서 참조 [11.1.2. Connection to a Production Database](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-connect-to-production-database)
{: .notice--info}


## Mybatis 연동

### 9. dependency

```xml 
<!-- Mybatis -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

### 10. mapper

```java 
@Repository
@Mapper
public interface WhmsMapper {
    public List<UserModel> selectUserInfo();
}
```

### 11.xml
resources>sql>whmsSQL.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.summit.whms.mapper.WhmsMapper">
    <select id="selectUserInfo" resultType="com.summit.whms.main.UserModel">
        SELECT name, email, passwd FROM users;
    </select>
</mapper>
```

### 11.service 호출

```java 
@Service
public class StudyServiceImpl implements StudyService {
    @Autowired
    public WhmsMapper whmsMapper;

    @Override
    public List<UserModel> getUserInfo() {
        return whmsMapper.selectUserInfo();
    }
}
```

### 12.Controller 호출

```java
@GetMapping("/userInfo")
public ModelAndView getUserInfo() {
    ModelAndView mv = new ModelAndView();
    List<UserModel> userList = studyService.getUserInfo();

    mv.addObject("list", userList);

    return mv;
}
```
