---
title: "JVM 옵션"
date: 2020-05-09 11:00:00 +0800
categories: 
  - java
tags:
  - java
  - jvm
sidebar:
  title: "IT"
  nav: "it-sidebar"
---

### 1. 전체 Heap 크기

```xml
-xms1024m
-xmx1024m
```

### 2. New 영역 크기

```xml
-Xmn512m
-XX:NewSize=512m
```

### 3. New영역과 Old영역 간 비율

```xml
-XX:NewRatio=3

Yong:Old 비율이 1:3이라는 의미 
```

### 4. New영역의 Eden, Survivor 영역비율

```xml
-XX:SurvivorRatio=8

Young 전제 2GB 에서 SurvivorRatio가 8이면 Eden영역이 1.6GB
Survivor영역이 0.4GB이고 Survivor 영역은 S1,S2로 2개의 영역으로
이루어져 있으므로 각각 0.2GB, 결과적으로 Eden과 Survivor 비율은 1:8

```

### 5. Perm 영역 크기

```xml 
-XX:PermSize=64m
-XX:MaxPermSize=64m
```

### 6. Metaspace 영역 크기

```xml
-XX:MetaspaceSize=256m
-XX:MaxMetaspaceSize=256m
```

### 7. Heap Dump 생성

```xml
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/logs001/java/heapdump/
```

### 8. promotion failed 

- cms gc에서 promotion failed 발생시 조치 방안

```xml

-Xmx4G: 가용 메모리크기가 된다면 Heap 사이즈를 증가
-XX:NewRatio: 전체 Heap에서 New영역 비율을 증가
-XX:PromotedPadding: 제공되는 패딩 양을 늘림
-XX:CMSInitiatingOccupancyFraction=60 -XX:+UseCMSInitiatingOccupancyOnly
cms gc 주기를 더 빨리 시작 

```