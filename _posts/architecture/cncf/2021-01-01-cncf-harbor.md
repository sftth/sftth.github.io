---  
title: Harbor Installation
excerpt: Harbor 설치 및 Trivy로 취약점 스캐닝 예제
date: 2021-03-13 00:00:00 +0800
last_modified_at: 2021-03-13 00:00:00 +0800
categories: cncf
header:
  teaser: /assets/images/01_teaser/burns.jpg
  overlay_image: /assets/images/01_teaser/burns.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/burns.jpg
  caption: "Photo by Christopher Burns on Unsplash"
---

### 1. 개요 
- 컨테이너 이미지를 저장, 재사용하려면 레지스트리가 필요함
- 퍼블릭 레지스트리로 docker hub가 있으나 기능 제약, 도커 빌드 속도 문제 등이 있음
- 폐쇄망인 경우에 레지스트리를 별도로 구성할 필요가 있음 

### 2. 방안
- 컨테이너 이미지 레지스트리를 구성하는 많은 방법중에 CNCF 졸업 프로젝트인 Harbor를 사용 
- 더불어 Trivy를 이용하여 컨테이너 이미지 보안 취약점 스캐닝을 함 

### 3. 제약사항
- 작업 환경:  GCP e2-medium(vCPU 2개, 4GB 메모리), OS: ubuntu-1604-xenial-v20210224
- 해당 환경에 docker, docker-compose가 설치 되어 있어야 함 [설치 참고](https://docs.docker.com/compose/install/)
- 작업 계정으로 docker. docker-compose 실행 가능해야 함 (sudo usermod -aG docker 사용계정)
- harbor 설치시 hostname을 설정해야 하는데 유료 dns 서비스 이용이 불가한 경우 테스트용으로 [duckdns](https://www.duckdns.org/) 사용 가능 

### 4. 설명

### 4.1 Harbor 설치
- [goharbor/harbor](https://github.com/goharbor/harbor/releases)에서 설치 파일 다운로드
  
  <img src="/assets/images/cncf/harbor001.png" width="400" height="600" alt="harbor">

- 설치파일 압축 풀기

  ```sh 
  tar xvf harbor-online-installer-v2.2.0.tgz
  ```
- 설정에 앞써 몇가지 설정을 변경을 위해 harbor.yml.tmpl 파일을 편집함 

  ```sh 
  cd ~/harbor
  vi harbor.yml.tmpl
  ```

- hostname 변경
 
  ```sh 
  hostname: sftth-harbor.duckdns.org <-원하는 DNS로 변경 
  ```

- https 비활성화

  ```sh 
  # port 443
  # certificate: ...
  # private_key: ...
  ```

- harbor 관련 데이터 저장 위치 지정

  ```sh 
  data_volume: /data001/harbor <-- 해당 디렉토리는 미리 만들어 둠
  ```
  
  **참고** 디렉토리 생성 방법 mkdir -p /data001/harbor && chown -R 사용자:그룹 /data001/
  {: .notice--info}

- harbor 로그 경로 위치 지정

  ```sh
  log:
   ...
  location: /logs001/harbor <-- 디렉토리는 미리 만듬  
  ```

- harbor.yml.tmpl 을 harbor.yml 로 명칭 변경 후 install 수행

  ```sh 
  mv harbor.yml.tmpl harbor.yml 
  ./install.sh --with-trivy 
  ```

  **참고** 설치 수행시 docker-compose Permisson denied 문제 발생하는 경우 **sudo ./install.sh --with-trivy** 수행 
  {: .notice--info}
  
- 설치 완료 화면 (초기 로그인 정보: admin / Harbor12345)

  <img src="/assets/images/cncf/harbor002.png" width="400" height="600" alt="harbor">

- docker 커멘드로 확인한 harbor 관련 컨테이너 리스트 
  
  ```sh 
  CONTAINER ID   IMAGE                                  COMMAND                  CREATED      STATUS                            PORTS                       NAMES
  e95812a157fa   goharbor/nginx-photon:v2.2.0           "nginx -g 'daemon of…"   2 days ago   Up 2 seconds (health: starting)   0.0.0.0:80->8080/tcp        nginx
  a5845808b380   goharbor/harbor-jobservice:v2.2.0      "/harbor/entrypoint.…"   2 days ago   Up 14 minutes (healthy)                                       harbor-jobservice
  73e9ad801f4c   goharbor/harbor-core:v2.2.0            "/harbor/entrypoint.…"   2 days ago   Up 14 minutes (healthy)                                       harbor-core
  cd7e92193266   goharbor/trivy-adapter-photon:v2.2.0   "/home/scanner/entry…"   2 days ago   Up 14 minutes (healthy)                                       trivy-adapter
  de8d704c7de4   goharbor/registry-photon:v2.2.0        "/home/harbor/entryp…"   2 days ago   Up 14 minutes (healthy)                                       registry
  3a1d9db09edc   goharbor/redis-photon:v2.2.0           "redis-server /etc/r…"   2 days ago   Up 14 minutes (healthy)                                       redis
  f2fc1393fdb4   goharbor/harbor-portal:v2.2.0          "nginx -g 'daemon of…"   2 days ago   Up 43 seconds (healthy)                                       harbor-portal
  fcfc39246530   goharbor/harbor-db:v2.2.0              "/docker-entrypoint.…"   2 days ago   Up 14 minutes (healthy)                                       harbor-db
  a7c95e553a48   goharbor/harbor-registryctl:v2.2.0     "/home/harbor/start.…"   2 days ago   Up 54 seconds (healthy)                                       registryctl
  c3f6be4ab220   goharbor/harbor-log:v2.2.0             "/bin/sh -c /usr/loc…"   2 days ago   Up 14 minutes (healthy)           127.0.0.1:1514->10514/tcp   harbor-log
  ```

### 4.2 sample 이미지 등록하기

- harbor가 컨테이너 이미지 레지스트리로 작동 가능한지 확인을 위해 sample 이미지를 등록해봄 

  ```sh 
  docker pull sftth/whms:1.2
  docker tag sftth/whms:1.2 sftth-harbor.duckdns.org/libray/whms:1.2
  docker login sftth-harbor.duckdns.org
  docker push sftth-harbor.duckdns.org/libray/whms:1.2
  ```
  
- 성공적인 등록 결과를 확인 할 수 있음 

  <img src="/assets/images/cncf/harbor004.png" width="400" height="600" alt="harbor">

### 4.3 이미지 취약점 스캔

- trivy를 통해 이미지 취약점 스캔 수행
- 스캔 방법은 스캔 대상 컨테이너 이미지 선택 ,[scan]버튼 클릭, 결과 확인 

  <img src="/assets/images/cncf/harbor003.png" width="400" height="600" alt="harbor">