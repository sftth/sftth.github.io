---
title: "Tomcat - Parameters"
#excerpt: AWS Autoscale 기본 설정  
date: 2020-05-09 11:00:00 +0800
last_modified_at: 2020-05-09 11:00:00 +0800
header:
  teaser: /assets/images/01_teaser/cat-paper.png
  overlay_image: /assets/images/01_teaser/cat-paper.png
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/cat-paper.png
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: tomcat
---

### tomcat 주요 파라미터

- Connectors

```xml
address
port
minSpareThreads
maxThreads
acceptCount
maxConnections
connectionTimeout
keepAliveTimeout
maxKeepAliveRequests
maxParameterCount
compression
```

- Session

```xml
maxActiveSessions
session-timeout
```

- JDBC Connection Pool

```xml
driverClassName
username
password
initialSize
maxActive
maxIdle
minIdle
maxWait
testOnConnect
testOnBorrow
testOnReturn
testWhileIdle
validationQuery
validationQueryTimeout
validationInterval
timeBetweenEvictionRunsMillis
minevictableIdelTimeMillis 

```