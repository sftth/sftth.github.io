---
title: "JVM 옵션"
excerpt: Java JVM 옵션
date: 2020-05-10-09 11:00:00 +0800
last_modified_at: 2020-05-09 00:00:00 +0800
categories: java
tag:
- java
header:
  teaser: /assets/images/01_teaser/michael.jpeg
  overlay_image: /assets/images/01_teaser/michael.jpeg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/michael.jpeg
  caption: "Photo by Michael Haddad on Unsplash"    
---

### 1. Heap 사이즈 최대/최소 설정
```sh 
-Xms1024m
-Xmx1024m
```

**참고** 
 - WAS의 경우 2~4GB 정도 설정
 - 독립 실행 배치는 125~256MB 정도 설정
 - 대량 데이터 로드하여 분석하는 시스템의 경우 10GB 이상 설정하기도 함 
 - 응답시간이 짧은 경우 New 영역을 늘려주면 Full GC 발생 횟수를 줄일 수 있음 
{: .notice--info}

### 2. New 영역 크기
```sh 
-Xmn512m
-XX:NewSize=512m
```

### 3. New영역과 Old영역 간 비율

  ```sh 
  -XX:NewRatio=2
  
  Yong:Old 비율이 1:2이라는 의미 
  ```

### 4. New영역의 Eden, Survivor 영역비율

  ```sh 
  -XX:SurvivorRatio=8
  
  Young 전제 2GB 에서 SurvivorRatio가 8이면 Eden영역이 1.6GB
  Survivor영역이 0.4GB이고 Survivor 영역은 S1,S2로 2개의 영역으로
  이루어져 있으므로 각각 0.2GB, 결과적으로 Eden과 Survivor 비율은 1:8
  ```
- Survivor 영역 크기 = New 영역 전체 / (SurvivorRatio + 2)
- UseAdaptiveSizePolicy는 New영역, Eden, Survivor 비율을 동적으로 조정하는 옵션   
- -XX:-UseAdaptiveSizePolicy 처리하여 빈번한 GC로 성능 저하 방지
- [CMS GC, NewRatio 튜닝하기](https://brunch.co.kr/@alden/47)

### 5. Perm 영역 크기

  ```sh  
  -XX:PermSize=64m
  -XX:MaxPermSize=64m
  ```
- Java 1.8 부터 Perm 영역 없어짐 

### 6. Metaspace 영역 크기

  ```sh 
  -XX:MetaspaceSize=256m
  -XX:MaxMetaspaceSize=256m
  ```

### 7. Heap Dump 생성

  ```sh 
  -XX:+HeapDumpOnOutOfMemoryError
  -XX:HeapDumpPath=/logs001/java/heapdump/
  ```

- Eclipse MAT(Memory Analyze Tool)가 우수한 Heap Dump 분석 도구로 사용 권고함

### 8. 기동모드

```sh 
-server
-client
```
- 운영환경에서는 기본적으로 server 모드로 기동
- client 모드의 경우 빠른 시작과 적은 메모리 사용에 최적화 되어 있음(64bit JDK에서는 사용 불가)

### 8. promotion failed 

- cms gc에서 promotion failed 발생시 조치 방안

  ```sh 
  -Xmx4G: 가용 메모리크기가 된다면 Heap 사이즈를 증가
  -XX:NewRatio: 전체 Heap에서 New영역 비율을 증가
  -XX:PromotedPadding: 제공되는 패딩 양을 늘림
  -XX:CMSInitiatingOccupancyFraction=60 -XX:+UseCMSInitiatingOccupancyOnly
  cms gc 주기를 더 빨리 시작 
  ```