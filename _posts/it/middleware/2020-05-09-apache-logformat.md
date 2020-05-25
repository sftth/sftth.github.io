---
title: "Apache - access log 옵션"
date: 2020-05-09 11:00:00 +0800
categories: 
  - apache
tags:
  - apache
  - accesslog
sidebar:
  title: "IT"
  nav: "it-sidebar"
---

### 1. access log

- 기본 포맷에 없는 응답시간 설정 추가

#### 1.1 기본

```xml
LogFormat "%h %l %u %t \"%r\ %>s %b" common
```

#### 1.2 %D 추가
- 요청을 처리하는 데 걸린 시간 (밀리초)

```xml
LogFormat "%h %l %u %t \"%r\ %>s %b %D" common
```

#### 1.3 %T 추가

- 요청을 처리하는 데 걸린 시간 (초)

```xml
LogFormat "%h %l %u %t \"%r\ %>s %b %T" common
```