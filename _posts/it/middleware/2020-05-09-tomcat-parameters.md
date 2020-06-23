---
title: "Tomcat - Parameters"
date: 2020-05-09 11:00:00 +0800
categories: tomcat
sidebar:
  nav: "it-sidebar"
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