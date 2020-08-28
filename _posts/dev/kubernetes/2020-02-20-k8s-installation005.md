---
title: k8s 기본 1-6
excerpt: Kubeadm을 이용한 쿠버네티스 클러스터링 구성하기 
date: 2020-02-20 04:00:00 +0800
last_modified_at: 2020-08-25 00:00:00 +0800
header:
  teaser: https://live.staticflickr.com/65535/50118270208_9de7c7ed9d_c.jpg
  overlay_image: https://live.staticflickr.com/65535/50118270208_9de7c7ed9d_c.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/65535/50118270208_9de7c7ed9d_c.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: kubernetes
---

### 환경

GCP(Google Cloud Platform)
Ubuntu 16.04 LTS

master 1대
worker 2대

CPU 2core
MEM 7.4GB

master 10.128.0.14
work1  10.128.0.15
work2  10.128.0.16

### VM 생성

<img src="https://live.staticflickr.com/65535/50267786546_efbc623403_b.jpg" width="628" height="1024" alt="kubeadm001">

master vm 생성

#### 도커 설치

이전 버전 삭제 
```sh 
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

레파지토리 셋업용 사전 작업 

```sh 
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

도커 공식 GPG 키 등록

```sh 
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

핑거프린트 등록

```sh 
$ sudo apt-key fingerprint 0EBFCD88
```

레파지토리 셋업 

```sh 
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

도커 엔진 설치

```sh 
 $ sudo apt-get update
 $ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

<script id="asciicast-yFVn5jP94cwPSrucEVXO2TIZG" src="https://asciinema.org/a/yFVn5jP94cwPSrucEVXO2TIZG.js" async></script>
<!--[![asciicast](https://asciinema.org/a/yFVn5jP94cwPSrucEVXO2TIZG.svg)](https://asciinema.org/a/yFVn5jP94cwPSrucEVXO2TIZG) -->
참고: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

#### kubeadm 설치

마스터용 VM에 kubeadm 설치 

```sh 
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

<script id="asciicast-FskxNZm16D2zsY0vVh0foWIDR" src="https://asciinema.org/a/FskxNZm16D2zsY0vVh0foWIDR.js" async></script>

<!-- [![asciicast](https://asciinema.org/a/FskxNZm16D2zsY0vVh0foWIDR.svg)](https://asciinema.org/a/FskxNZm16D2zsY0vVh0foWIDR) -->

참고: [Installing kubeadm, kubelet, kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

설치 완료 후 vm 종료

### VM 이미지 생성

지금까지 마스터 용 VM에 도커 및 kubeadm 설치 함
master VM 이미지를 생성및 활용하여 master 노드와 같은 설정을 가진 2대의 worker 노드 생성 

#### master VM 이미지 생성 
<img src="https://live.staticflickr.com/65535/50267897011_62b224c6ec_b.jpg" width="1024" height="239" alt="kubeadm002">

<img src="https://live.staticflickr.com/65535/50267896951_6e16d54f2d_b.jpg" width="826" height="1024" alt="kubeadm003">

#### 이미지를 활용하여 worker VM 생성
<img src="https://live.staticflickr.com/65535/50268122007_dc7071b203_b.jpg" width="1024" height="159" alt="kubeadm004">

#### master 노드 key 생성 및 메타데이터 등록 

```sh 
summit@master:~$ sudo su
root@master:/home/summit# ssh-keygen -t rsa -b 4096
```
<img src="https://live.staticflickr.com/65535/50268133097_95e03cf9e3_b.jpg" width="1024" height="335" alt="kubeadm005">

마스터 노드에서 worker 노드용 vm hostname 호출이 되면 등록 성공

```sh 
root@master:/home/summit# ssh work1 hostname
The authenticity of host 'work1 (10.142.0.3)' can't be established.
ECDSA key fingerprint is SHA256:XXXXX.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'work1,10.142.0.3' (ECDSA) to the list of known hosts.
work1
root@master:/home/summit# ssh work2 hostname
The authenticity of host 'work2 (10.142.0.4)' can't be established.
ECDSA key fingerprint is SHA256:XXXXX.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'work2,10.142.0.4' (ECDSA) to the list of known hosts.
work2
```

#### kubeadm init
마스터 노드에서 다음 명령어 실행

```sh
$ kubeadm init

<중략>
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.142.0.2:6443 --token s4wlwq.3o1fnvkfazoojpwg \
    --discovery-token-ca-cert-hash sha256:0f2cb0..... 
```

#### master 노드 client 설정

마스터 노드에서 root 계정을 사용자 계정으로 변경 후 아래 명령어 수행 

```sh 
summit@master:~$ mkdir -p $HOME/.kube
summit@master:~$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
summit@master:~$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

이를 통해 마스터 노드에서 kubectl 명령어 수행 가능 

```sh 
summit@master:~$ kubectl get node
NAME     STATUS     ROLES    AGE     VERSION
master   NotReady   master   6m41s   v1.18.8
```

위에 kubectl 명령어 수행 시 마스터 노드에 대한 정보 표시 확인 
아직 표시되지 않는 work1, work2 노드 추가 필요

### worker node 추가하기

아래 join 스크립트를 work1, work2 VM에서 root 계정으로 수행

```sh 
kubeadm join 10.142.0.2:6443 --token s4wlwq.3o1fnvkfazoojpwg \
    --discovery-token-ca-cert-hash sha256:0f2cb0bfba.... 
```

각각 VM에서 수행 후 다시 마스터 노드에서  kubectl 명령어로 확인 

```sh 
summit@master:~$ kubectl get node
NAME     STATUS     ROLES    AGE   VERSION
master   NotReady   master   10m   v1.18.8
work1    NotReady   <none>   61s   v1.18.8
work2    NotReady   <none>   56s   v1.18.8
```

### Network Plugin 설치

마스터 노드 및 worker 노드 생성까지 완료 되었으나 Status가 아직 NotReady 상태임
이를 해결하기 위해서는 CNI 용 네트워크 플러그인 설치가 필요함

참고:[Network add-on](https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model)

네트워크 플러그인의 종류는 많으나 이번에는 Weave Net으로 선정하여 설치함 

#### Weave Net 설치
마스터 노드에서 아래  kubectl 명령어 통해 설치 

```sh 
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

노드 상태 확인 

```sh 
summit@master:~$ kubectl get nodeNAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   15m     v1.18.8
work1    Ready    <none>   5m50s   v1.18.8
work2    Ready    <none>   5m45s   v1.18.8
```

### Service NodePort 호출 테스트

kubespray를 통해 클러스터링 구성시에는 CNI 설정 문제로 서비스에서 다른 노드에 있는 파드호출을 하지 못하는 문제 있었음.
kubeadm으로 설치시에는 문제 발생 여부 확인하고자 deployment 및 nodeport 유형 service 생성하여 호출 테스트 수행.

아래 deployment, pods 및 service 생성 확인

```sh 
NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/whms-deployment-5d545b8597   3         3         3       114m

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/whms-deployment   3/3     3            3           114m

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        139m
service/whms-nodeport   NodePort    10.98.49.123   <none>        80:30080/TCP   113m

NAME                                   READY   STATUS    RESTARTS   AGE
pod/whms-deployment-5d545b8597-b8555   1/1     Running   1          114m
pod/whms-deployment-5d545b8597-bcqmj   1/1     Running   1          114m
pod/whms-deployment-5d545b8597-cnmps   1/1     Running   1          114m
```

jmeter로 서비스 컨트롤러 호출 결과 서로 다른 노드에 생성된 파드 호출 시 정상 동작 확인

<img src="https://live.staticflickr.com/65535/50268238232_588cb5ef10.jpg" width="500" height="484" alt="kubeadm006">