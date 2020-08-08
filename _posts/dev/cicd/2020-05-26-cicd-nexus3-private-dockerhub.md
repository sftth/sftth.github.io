---
title: "CICD - Nexus 3 Private Docker Hub"
date: 2020-05-26 00:00:00 +0800

categories: 
  - cicd
tags:
  - nexus 3
  - registry
sidebar:
  title: "Development"
  nav: "dev-sidebar"
---

Window 10 플랫폼에 설치된 Nexus 3를 활용한 Private Docker Hub 사용법 입니다. <br>

### 1. Installation
- Nexus 3 설치 방법은 [Nexus 3 Installation](https://sftth.github.io/cicd-nexus3-installation/)을 참고

### 2. Blob 생성

- 경로: 톱니바퀴 > Repository > Blob Stores > Create blob store
- docker-hosted 생성
- docker-hub 생성

### 3. Repository 생성

- 경로: 톱니바퀴 > Repository > Repositories > Create repository
- docker-hosted 생성

옵션 <br>
- Recipe 유형은 docker(hosted) 선택

| 항목 | 설정 |
| --- | --- |
| Name | docker-hosted |
| HTTP | 체크, 5000 |
| Enable Docker V1 API | 체크 |
| Blob store | docker-hosted |

- docker-hub 생성

옵션 <br>
- Recipe 유형은 docker(proxy) 선택

| 항목 | 설정 |
| --- | --- |
| Name | docker-hub |
| Enable Docker V1 API | 체크 |
| Remote storage | https://registry-1.docker.io |
| Docker Index | Use Docker Hub |
| Blob store | docker-hub |

### 4. Realms 설정

- 경로: 톱니바퀴 > Realms
- Docker Bearer Token Realm을 Active로 이동 > Save


### 5. Docker http 예외 설정

- Window의 경우 Docker Desktop > Settings 메뉴 활용
- Settings > Docker Engine에 "insecure-registries" 설정

```sh 
{
...<중략>...
  "insecure-registries": [
    "192.168.0.18:5000"
  ],
...<중략>...
}
```

**Warning Notice:** 위에서 IP의 경우 127.0.0.1 또는 localhost로 설정 하면 안 됨.
{: .notice--warning}

- docker 재시작

리눅스의 경우 <br>

- /etc/docker/daemon.json 파일 생성
- insecure-registries 설정
- service docker restart


### 6. docker push

- docker login

```sh
docker login 192.168.0.18:5000
Username: admin
Password: ${nexus login password}
....
Login Succeeded
```

- docker tag

```sh 
docker tag ${image id} 192.168.0.18:5000/jacob:1.0
```

- docker push

```sh 
docker push 192.168.0.18:5000/jacob:1.0
...<중략>
```

- push된 이미지 확인

```sh 
Browse > docker-hosted
```

### 7. docker pull

```sh 
docker pull 192.168.0.18:5000/jacob:1.0
...<중략>

docker images -a
```

### 8. SSL 적용

- TO-DO

참고 <br>
