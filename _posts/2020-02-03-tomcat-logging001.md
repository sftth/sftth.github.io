---
title: "Tomcat 로그설정 - Catalina.out 관리 방법 "
date: 2020-01-21 11:00:00 +0800
categories: Tomcat
sidebar:
  nav: "it-sidebar"
---

Tomcat은 기본적으로 Catalina.out이라는 파일에 로그를 쌓는다. 이 로그 파일은 딱 한 개 생성되고 
로그 정보를 계속 쌓게 되어 그 크기 제한 없이 한 없이 커진다. 황당한 이야기 같지만 사실이다. <p>
따라서 Tomcat 설치 후 Catalina.out 파일 관리를 위한 설정이 필요하다. 이 포스팅은 관리하지 않으면
무한히 커지는 Catalina.out 파일 관리 방법에 관한 것이다. <p>

## 기본사항
- 설명을 위해 Tomcat은 아래 경로에 설치 되었다고 가정 함.(이하 TOMCAT_HOME)

  ```sh
  /engn001/tomcat/9.0/servers/ist_8180
  ```

## Path
- Tomcat 에서 Catalina.out 파일 생성에 관한 설정은 아래 파일에 존재 함.

  ```sh
  TOMCATHOME/bin/catalina.sh
  ```

## Catalina.out 제거
- catalina.sh 파일에서 Catalina.out 생성 부분을 수정하여 Catalina.out 파일 생성을 중지함. <p>

- 수정 전(Tomcat 설치 디렉토리 하위 logs 디렉토리에 파일 생성)
    
    ```sh
    if [ -z "$CATALINA_OUT" ] ; then
      CATALINA_OUT="$CATALINA_BASE"/logs/catalina.out
    fi
    ```

- 수정 후(catalina.out 생성 제거)

    ```sh
    if [ -z "$CATALINA_OUT" ] ; then
      CATALINA_OUT=/dev/null
    fi
    ```

## catalina.YYYY.MM-DD.log 제거
- Tomcat은 Catalina.out 파일과 더불어 catalina.YYYY.MM-DD.log 형태의 로그가 생성 됨.
- 해당 로그는 1일 단위로 생성되지만 Tomcat에서 생성하는 로그 외에 응용로그(log4j2, logback)은 
기록하지 못하므로 활용성이 낮아서 아래 경로에 존재하는 logging.properties 파일을 삭제하여 생성을 막음.

1. 별도의 인스턴스 구분 없는 경우

    ```sh
    TOMCAT_HOME/conf/logging.properties
    ```

2. 별도의 인스턴스를 사용하는 경우(인스턴스 경로가 TOMCAT_HOME/servers/ist_8180/ 인 경우)
    
    ```sh
    TOMCAT_HOME/servers/ist_8180/conf/logging.properties
    ```

## Catalina.out 파일 Rolling
- Catalina.out 파일을 생성하고 싶은 경우는 Catalina.out 파일을 Rolling 설정하여 생성 및 관리 할 수 있음.
- 설정 프로세스는 아래와 같음

1. tomcat 파일 생성

- 디렉토리 이동 및 파일 생성

    ```sh
    [root@ ]# cd /etc/logrotate.d/
    [root@ logrotate.d]# vi tomcat
    ```

- tomcat 파일 내부 스크립트 내용

    ```sh
     /logs001/tomcat/9.0/ist_8180/server.log { // Catalina.out 로그파일 경로
     copytruncate                              // 기존 파일 백업 및 삭제
     daily                                     // 로그파일을 날짜별로 Rolling
     rotate 30                                 // 최대 30일까지만 생성
     compress                                  // 로그파일 gzip 압축
     missingok                                 // 로그파일 부재시 무시함
     notifempty                                // 로그파일 부재시 신규 생성 하지 않음
     dateext                                   // 순환된 로그파일 날짜 확장자
    }
    ```

2. crontab에 tomcat 파일 등록

- 파일 열기
    ```sh
    [root@ logrotate.d]# vi /etc/crontab
    ```

- crontab 작성

    ```sh
    SHELL=/bin/bash
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root
    
    # For details see man 4 crontabs
    
    # Example of job definition:
    # .---------------- minute (0 - 59)
    # |  .------------- hour (0 - 23)
    # |  |  .---------- day of month (1 - 31)
    # |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    # |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    # |  |  |  |  |
    # *  *  *  *  * user-name  command to be executed
      0  0  *  *  *  root run-parts/etc/cron.daily
    ```

3. 처리 프로세스
- /etc/crontab 스케쥴 동작 > /etc/logrotate.conf 실행 > /etc/logrotate.d 참조 > tomcat 실행

4. 검증 방법
- 아래 스크립트를 수동 실행하여 설정이 잘 동작 하는지 확인

    ```sh
    /usr/sbin/logrotate -f/etc/logrotate.conf
    ```

5. 생성 결과 확인
- 기존 Catalina.out은 압축되고 신규로 Catalina.out 파일 생성 확인

    ```sh
    etc-user etc-user 0 Feb  3 07:34 Catalina.out
    etc-user etc-user 101491 Feb  3 07:03 Catalina.out-20200203.gz
    ```

- crontab 설정된 스케쥴 실행 로그
    ```sh
    Feb  4 00:40:01  CROND[5713]: (root) CMD (run-parts /etc/cron.daily)
    Feb  4 00:40:01  run-parts(/etc/cron.daily)[5713]: starting logrotate
    Feb  4 00:40:01  run-parts(/etc/cron.daily)[5723]: finished logrotate
    Feb  4 00:40:01  run-parts(/etc/cron.daily)[5713]: starting man-db.cron
    Feb  4 00:40:02  run-parts(/etc/cron.daily)[5736]: finished man-db.cron
    Feb  4 00:40:02  run-parts(/etc/cron.daily)[5713]: starting mlocate
    Feb  4 00:40:02  run-parts(/etc/cron.daily)[5747]: finished mlocate
    ```
