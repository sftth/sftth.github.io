---
title: "Java HeapDump"
excerpt: Java HeapDump 생성 방법 
date: 2020-05-10 11:00:00 +0800
last_modified_at: 2020-05-10 00:00:00 +0800
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

### 1. 생성 방법

- Oracle Hopspot
```sh
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

```sh 
export IBM_HEAP_DUMP=true
export IBM_HEAPDUMP=true
Unix: kill -3 PID 
Win: Ctrl + Break 
```

### 2. 분석도구

- Eclipse Memory Analyzer
- IBM HeapAnalyzer

