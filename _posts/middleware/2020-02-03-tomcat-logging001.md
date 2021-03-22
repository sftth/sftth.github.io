---
title: "Tomcat 로그설정 - Catalina.out 관리 방법 "
#excerpt: AWS Autoscale 기본 설정  
date: 2020-02-03 11:00:00 +0800
last_modified_at: 2020-02-03 11:00:00 +0800
header:
  teaser: /assets/images/01_teaser/cat-black.png
  overlay_image: /assets/images/01_teaser/cat-black.png
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/cat-black.png
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: tomcat  
---

Tomcat은 Catalina.out에 로그를 남긴다. 이 파일은 단일 파일로 생성되고 
별도의 관리를 하지 않으면 크기 제한 없이 한 없이 커진다. 따라서 Catalina.out 파일은 시스템 안정성
을 위한 관리가 필요하다. 이 포스팅은 관리하지 않으면 무한히 커지는 Catalina.out 파일 관리 방법에 관한 것이다. <p>

## 기본 사항
- 포스팅 설명을 위해 Tomcat설치 경로는 아래에 명시된 경로로 가정하고 이하 TOMCAT_HOME으로 명시
  ```sh
  /engn001/tomcat/9.0/servers/ist_8180
  ```
  
## catalina.sh
- Catalina.out 파일 생성 설정은 catalina.sh에 있고 경로는 다음과 같음
  ```sh
  TOMCATHOME/bin/catalina.sh
  ```
  
## 관리방법1: Catalina.out 제거
- 첫번째 관리 방법으로 catalina.sh 파일에서 Catalina.out 생성 부분을 수정하여 Catalina.out 파일 생성을 중지함.
- 수정 전
   - TOMCAT_HOME/logs에 Catalina.out 생성  
    ```sh
    if [ -z "$CATALINA_OUT" ] ; then
      CATALINA_OUT="$CATALINA_BASE"/logs/catalina.out
    fi
    ```
  
- 수정 후
   - catalina.out 생성 경로에 /dev/null 설정하여 생성을 차단
    ```sh
    if [ -z "$CATALINA_OUT" ] ; then
      CATALINA_OUT=/dev/null
    fi
    ```
  
## 관리방법2: Rolling Catalina.out
- 두번째 방법은 OS의 기능을 활용하여 Catalina.out 파일을 Rolling 할 수 있음.
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
    - Rolling 수행 프로세스는 먼저 /etc/crontab 스케쥴 동작 이 설정된 시간에 맞춰 일어나고 다음으로
     /etc/logrotate.conf 실행 > /etc/logrotate.d 참조 및 최종적으로 tomcat 스크립트가 실행 됨
    
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
    
    - 더불어 vi /var/log/cron 로그를 확인 하면 아래와 같이 설정된 스케쥴 시점에 cron.daily 실행 로그를
      확인 할 수 있음
      
        ```sh
        Feb  4 00:40:01  CROND[5713]: (root) CMD (run-parts /etc/cron.daily)
        Feb  4 00:40:01  run-parts(/etc/cron.daily)[5713]: starting logrotate
        Feb  4 00:40:01  run-parts(/etc/cron.daily)[5723]: finished logrotate
        Feb  4 00:40:01  run-parts(/etc/cron.daily)[5713]: starting man-db.cron
        Feb  4 00:40:02  run-parts(/etc/cron.daily)[5736]: finished man-db.cron
        Feb  4 00:40:02  run-parts(/etc/cron.daily)[5713]: starting mlocate
        Feb  4 00:40:02  run-parts(/etc/cron.daily)[5747]: finished mlocate
        ```

## Appendix: catalina.YYYY.MM-DD.log 제거
- Tomcat은 Catalina.out 파일과 더불어 catalina.YYYY.MM-DD.log 형태의 로그가 생성 됨.
- 해당 로그는 1일 단위로 Rolling되지만 Tomcat에서 생성하는 로그 외에 응용로그(log4j2, logback)은 
기록하지 못하므로 활용성이 낮음
- 아래 경로에 존재하는 logging.properties 파일을 삭제하여 생성을 막음.

  - 경로1: 별도의 인스턴스 구분 없는 경우

    ```sh
    TOMCAT_HOME/conf/logging.properties
    ```

  - 경로2: 별도의 인스턴스를 사용하는 경우(인스턴스 경로가 TOMCAT_HOME/servers/ist_8180/ 인 경우)
    
    ```sh
    TOMCAT_HOME/servers/ist_8180/conf/logging.properties
    ```

## 참고 자료

logrotate.d 설정 및 cron 로그 확인: https://055055.tistory.com/6
