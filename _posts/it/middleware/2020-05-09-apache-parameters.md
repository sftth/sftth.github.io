---
title: "Apache - Parameters"
date: 2020-05-09 11:00:00 +0800
categories: apache
sidebar:
  nav: "it-sidebar"
---

### apache 주요 파라미터

- MPM prefork 방식

```xml 
- StartServers 2 // 시작시 작성된 하위 서버 프로세스 수 설정 
- MaxSpareServers 10 //최대 유휴 하위 서버 프로세스 수 설정 
- MinSpareServers 5 //최소 유휴 하위 서버 프로세스 수 설정 
```

- MPM worker 방식

```xml 
- ServerLimit        16  //활성 자식 프로세스 수에 대한 하드 제한 
- StartServers       2   //처음 시작할 프로세스 수
- MaxRequestWorkers  150 //동시에 제공 될 수 있는 최대 클라이언트 수 
- MinSpareThreads    25  //유휴 쓰레드 최소값
- MaxSpareThreads    75  //유휴 쓰레드 최대값 
- ThreadsPerChild    25  
- ThreadLimit        25  //서버 쓰레드 수의 하드 한계 
```

- MPM event 방식

**Primary Notice:** 
이 MPM은 HTTP에서 'keep alive problem'을 수정하려고합니다. 클라이언트가 첫 번째 요청을 완료 한 후에는 연결을 열어두고 동일한 소켓을 사용하여 추가 요청을 보내고 TCP 연결을 만드는 데 상당한 오버 헤드를 줄일 수 있습니다. 그러나 Apache HTTP Server는 전통적으로 클라이언트에서 데이터를 기다리는 전체 하위 프로세스 / 스레드를 유지하므로 자체 단점이 있습니다. 이 문제를 해결하기 위해이 MPM은 각 프로세스에 전용 리스너 스레드를 사용하여 청취 소켓, 연결 유지 상태에있는 모든 소켓, 처리기 및 프로토콜 필터가 작업을 수행 한 소켓 및 나머지 만 처리하는 소켓을 모두 처리합니다. 해야 할 일은 클라이언트에 데이터를 보내는 것입니다.
비 차단 소켓과 APR에 의해 노출 된 최신 커널 기능 (Linux의 epoll과 같은)을 활용하는이 새로운 아키텍처는 더 이상 천둥 무리 문제를 피하도록 구성된 mpm-accept Mutex를 필요로하지 않습니다.
단일 프로세스 / 스레드 블록이 처리 할 수있는 총 연결 량은 AsyncRequestWorkerFactor 지시문에 의해 조정됩니다.
{: .notice--primary}

```xml 
ThreadsPerChild = 10
ServerLimit     = 4
AsyncRequestWorkerFactor = 2
MaxRequestWorkers = 40

idle_workers = 4 (average for all the processes to keep it simple)

max_connections = (ThreadsPerChild + (AsyncRequestWorkerFactor * idle_workers)) * ServerLimit
                = (10 + (2 * 4)) * 4 = 72
```

```xml 
ThreadsPerChild   = 10
ServerLimit       = 4
MaxRequestWorkers = 40
AsyncRequestWorkerFactor = 2

If all the processes have all threads idle then:

idle_workers = 10

We can calculate the absolute maximum numbers of concurrent connections in two ways:

max_connections = (ThreadsPerChild + (AsyncRequestWorkerFactor * idle_workers)) * ServerLimit
                = (10 + (2 * 10)) * 4 = 120

max_connections = (AsyncRequestWorkerFactor + 1) * MaxRequestWorkers
                = (2 + 1) * 40 = 120
```