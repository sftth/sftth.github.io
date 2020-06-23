---
title: "Java - HeapDump"
date: 2020-05-10 11:00:00 +0800
categories: 
  - java
tags:
  - java
  - heapdump
sidebar:
  title: "IT"
  nav: "it-sidebar"
---

### 1. 생성 방법

- Oracle Hopspot

```xml
5.0 이하 
-XX:+HeapDumpOnCtrlBreak 
Unix: kill -3 PID 
Win: Ctrl + Break 

6.0 이상
jmap 이용
jmap -dump:format=b, file=파일경로 PID
```

- JRockit 

```xml 
-XX:+HeapDumpOnCtrlBreak
Unix: kill -3 PID 
Win: Ctrl + Break 
```

- IBM

```xml
export IBM_HEAP_DUMP=true
export IBM_HEAPDUMP=true
Unix: kill -3 PID 
Win: Ctrl + Break 
```

### 2. 분석도구

- Eclipse Memory Analyzer
- IBM HeapAnalyzer

