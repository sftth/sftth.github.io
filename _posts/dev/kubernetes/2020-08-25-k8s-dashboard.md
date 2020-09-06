---
title: K8s 기본-8.Dashboard
excerpt: Kubernetes Dashboard
date: 2020-08-25 01:00:00 +0800
last_modified_at: 2020-08-25 00:00:00 +0800
categories: kubernetes
header:
  teaser: https://live.staticflickr.com/65535/49986203617_8b099459c5_k.jpg
  overlay_image: https://live.staticflickr.com/65535/49986203617_8b099459c5_k.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/65535/49986203617_8b099459c5_k.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

### 개요

- 지금까지 콘솔을 통해 다뤘던 쿠버네티스를 UI기반으로 다룰 수 있도록 Dashboard를 설정하는 방법을 제공함

  참고 [Kubernetes Dashboard Github](https://github.com/kubernetes/dashboard)

### 설치

- 대시보드 생성을 위해서는 Kubernetes Dashboard Github에서 제공하는 탬플릿을 실행 시켜야 함
- 아래 스크립트를 master 노드에서 실행 시킴
```sh 
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
```

- 스크립트 실행 뒤에 아래와 같이 kubectl 명령어로 dashboard service 컨트롤러 생성을 확인함
- dashboard service는 CluserIP 유형으로 생성 되는데 LoadBalancer 유형으로 편집하여 외부 접속이
가능하도록 함.
```sh 
summit@master:/engn001/kube/tmp$ kubectl get svc -n kubernetes-dashboard
NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP      10.98.22.159    <none>           8000/TCP        23h
kubernetes-dashboard        LoadBalancer   10.105.70.243   34.121.XXX.XXX   443:31797/TCP   23h
```

  참고 [LoadBalancer 유형 설정](/_posts/dev/kubernetes/2020-02-28-k8s-svc002.md) 

### 접근

- kubernetes dashboard는 기본적으로 https 기반으로 접근 가능함
- 아래와 같이 insecure 옵션을 통해 http로 접근할 수 있으나 사용자 생성 및 로그인 기능을 
사용할 수 없기 때문에 권장하지 않음 
- insecure 옵션 적용 방법

    ```sh 
    args:
     # - --auto-generate-certificates
       - --enable-insecure-login
       - --enable-skip-login
       - --insecure-bind-address=0.0.0.0
       - --insecure-port=9090
       - --namespace=kubernetes-dashboard
    ```
  참고 [Accessing Dashboard](https://github.com/kubernetes/dashboard/blob/master/docs/user/accessing-dashboard/README.md#login-not-available)

### 사용자 생성

- kubernetes dashboard는 RBAC 기반 사용자 접근을 제어함

#### 서비스 어카운트 생성 

- 서비스 어카운트 생성 탬플릿 

    ```sh 
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin-user
      namespace: kubernetes-dashboard
    ```

#### 클러스터 롤 바인딩

- 클러스터 롤 바인딩 

    ```sh 
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: admin-user
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: cluster-admin
    subjects:
    - kind: ServiceAccount
      name: admin-user
      namespace: kubernetes-dashboard
    ```

#### Bearer 토큰 확보

- Bash인 경우 
```sh 
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```

- Powershell인 경우 
```sh 
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | sls admin-user | ForEach-Object { $_ -Split '\s+' } | Select -First 1)
```

참고[Create An Authentication Token](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)

### 접속 화면

- kubernetes dashboard은 사용자 생성 과정에서 생성한 Bearer 토큰을 통해 로그인 할 수 있음 <br>
  <img src="/assets/images/kubenetes/k8s-dash001.png" width="500" height="484" alt="k8s-svc-lb">

- 최종적으로 로그인에 성공하면 지금까지 커멘드로 작업했던 사항들을 간단하게 나마 UI를 통해 진행할 수 있음 <br>
  <img src="/assets/images/kubenetes/k8s-dash002.png" width="500" height="484" alt="k8s-svc-lb">

