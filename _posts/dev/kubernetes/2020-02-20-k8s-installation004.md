---
title: k8s 기본 1-5
excerpt: Kubespray를 이용한 쿠버네티스 클러스터링 구성하기 
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
쿠버네티스를 직접 구성해야 할 때를 대비하여 직접 구성하는 방법을 알아보겠습니다. <br>

### 1. 대표적인 도구

#### 1.1 Kubeadm

- 쿠버네티스에서 공식 제공하는 클러스터 생성/관리 도구
- 여러 대의 마스터 노드 구성에 로드밸런서를 두는 구조

#### 1.2 Kubespray

- 상용 서비스에 적합한 보안성과 고가용성이 있는 오픈 소스 프로젝트
- 서버 환경 설정 자동화 도구인 앤서블 기반으로 개발
- ingress-nginx, helm, cephfs 등 추가 구성 요소를 클러스터에 실행하는 역할 수행

### 2. 실습 인프라 구성

#### 2.1 VM 생성

실습은 kubespray를 설치하는 것으로 하고 환경은 GCP로 하겠습니다.
Google Cloud Platform에 접속하여 VM 인스턴스 만들기를 클릭합니다. <br>

![KUB24001](/assets/images/kubenetes/KUB24001.png)

실습에서 Ubuntu 16.04 LTS를 사용 할 예정으로 부팅 디스크는 Ubuntu 16.04 LTS에 버전에 맞춰서 설정합니다 <br>

![KUB24001](/assets/images/kubenetes/KUB24002.png)

인스턴스 생성은 총 5개를 만듭니다.

![KUB24001](/assets/images/kubenetes/KUB24003.png)

*참고 사항** GCP는 최근에 서울 리전이 생겼는데 서울 리전을 선택해서 인스턴스를 만들었더니만 오류가 납니다. 
아직 안되나 봅니다.
{: .notice--primary}

#### 2.2 사용자 생성

**Danger Notice:** 아래에는 kubespray 설치를 위한 계정 설정 방법을 설명합니다. 그러나 작업을 진행하면서 보니 root 계정으로 
작업을 할 수밖에 없음을 알게되었습니다. 그래서 아래 설명은 참고만 하시고 모든 작업은 root계정으로 수행해야 합니다.
차후에 사용자 계정으로 수행할 방법을 찾으면 업데이트 예정입니다. 
{: .notice--danger}

Kubespray 설치에 사용할 계정 정보를 생성합니다. <br>

```sh 
****@instance-1:~$ sudo groupadd midadm
****@instance-1:~$ sudo useradd -m -g midadm -s /bin/bash midadm
****@instance-1:~$ sudo passwd midadm
****@instance-1:~$ cd /
****@instance-1:/$ sudo mkdir /engn001
****@instance-1:/$ sudo chown midadm:midadm /engn001/
****@instance-1:/$ su - midadm
Password: 
midadm@instance-1:~$ cd /engn001/
```

midadm 계정은 sudo 사용을 할 수 없습니다. 그래서 아래 설정을 통해 sudo 사용 권한을 부여합니다. <br>

```sh 

****@instance-1:/$ sudo su
root@instance-1:/# chattr -i /etc/sudoers
root@instance-1:/# chmod u+w /etc/sudoers
root@instance-1:/# vi /etc/sudoers

</etc/sudoers 내부>

# User privilege specification
root    ALL=(ALL:ALL) ALL
midadm  ALL=(ALL:ALL) ALL

```

#### 2.3 SSH 키 생성과 배포

instance-1 노드를 마스터노드#1로 정하고 해당 노드와 나머지 VM간 ssh 접근을 위해 설정을 수행합니다. <br>
먼저, 마스터 노드(instance-1)에 콘솔로 접속합니다. 접속한 마스터 노드의 ssh 키를 생성합니다. <br>

```sh 
root@instance-1:~$ ssh-keygen -t rsa -b 4096 -C "kube-inst01"
```

![KUB24001](/assets/images/kubenetes/KUB24004.png)

생성된 마스터 노드의 ~/.ssh 디렉토리 안에 id_rsa.pub 파일을 복사하여 Compute Engine > 메타데이터 메뉴에 등록합니다. <br>

```sh 
root@instance-1:~$ cat ~/.ssh/id_rsa.pub 
```

![KUB24001](/assets/images/kubenetes/KUB24005.png)

마스터 노드를 콘솔로 접속하여 SSH 원격 명령으로 호스트네임을 호출합니다. <br>
아래는 마스터 노드에서 instance-2 호스트네임을 호출하는 예입니다.

```sh 
root@instance-1:~$ ssh instance-2 hostname
The authenticity of host 'instance-2 (10.146.0.14)' can't be established.
ECDSA key fingerprint is SHA256:ToLyQGmXkK8ezD51a2mwz7i2C6Lp992XzGF9ecaGit0.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'instance-2,10.146.0.14' (ECDSA) to the list of known hosts.
instance-2
```

### 3. Kubespray 설치

#### 3.1 필요 패키지 설치

먼저 ubuntu 패키지 매니저를 최신 상태로 업데이트 합니다. <br>

```sh 
root@instance-1:~$ sudo apt update 
```

파이썬 패키지 매니저인 pip를 설치합니다. <br>
더불어, 설치 후에 버전을 확인합니다. <br>

```sh 
root@instance-1:~$ sudo apt -y install python-pip
root@instance-1:~$ pip install --upgrade pip
root@instance-1:~$ pip --version
WARNING: pip is being invoked by an old script wrapper. This will fail in a future version of pip.
Please see https://github.com/pypa/pip/issues/5599 for advice on fixing the underlying issue.
To avoid this problem you can invoke Python with '-m pip' instead of running pip directly.
pip 20.0.2 from /home/midadm/.local/lib/python2.7/site-packages/pip (python 2.7)
```

Kubespray를 git에서 clone 하기 위해 git을 설치합니다. <br>

```sh 
sudo apt -y install git
```

#### 3.2 kubespray 클론

kubespray를 git에서 클론합니다. <br>

```sh 
git clone https://github.com/kubernetes-sigs/kubespray.git
Cloning into 'kubespray'...
<중략...>
```

아래 명령으로 체크아웃을 합니다. <br>

```sh 
cd kubespray
git checkout -b v2.11.0
git status
```

git status 수행 결과가 On branch v2.11.0이면 의도했던 버전 지정이 완료 되었습니다. <br>

아래 명령으로 Kubespray에서 필요한 패키지를 설치합니다 <br>

```sh 
sudo pip install -r requirements.txt
```

**Warning Notice:** pip 수행 할 때 아래와 같은 문제 발생 할 수 있는데, 이 경우 locale 설정 문제로 
아래 해결책을 통해 해결합니.
{: .notice--warning}

```sh 
root@instance-1:~/kubespray$ sudo pip install -r requirements.txt 
Traceback (most recent call last):
  File "/usr/bin/pip", line 11, in <module>
    sys.exit(main())
  File "/usr/lib/python2.7/dist-packages/pip/__init__.py", line 215, in main
    locale.setlocale(locale.LC_ALL, '')
  File "/usr/lib/python2.7/locale.py", line 581, in setlocale
    return _setlocale(category, locale)
locale.Error: unsupported locale setting
```

***해결책*** <br>

- locale 파일 열기
- LC_ALL 설정 후 저장하기( :wq )

```sh
sudo vi /etc/default/locale

LC_ALL=en_US.UTF-8

sudo source /etc/default/locale
```

**Notice:** locale 설정 후에 적용이 안될 경우는 sudo reboot 수행 후에 
재시도해 봅니다.
{: .notice--info}

설치 후에 ansible 버전 확인하여 설치를 마무리 합니다. <br>

```sh 
ansible --version

ansible 2.7.16 ...<중략>...
```

### 4. Kubespray 설정

클러스터로 구성할 서버들의 정보와 설치 옵션을 설정합니다 <br>

#### 4.1 탬플릿 복사

./kubespray/inventory/sample 을 ./kubespray/inventory/mycluster 로 복사합니다 <br>

```sh 
cd /engn001/kubespray
cp -rfp inventory/sample inventory/mycluster
```

mycluster 디렉토리에는 group_vars와 inventory.ini 파일이 있습니다 <br>

```sh 
root@instance-1:/engn001/kubespray/inventory/mycluster$ ll
total 16
drwxrwxr-x 3 midadm midadm 4096 Mar 20 06:43 ./
drwxrwxr-x 5 midadm midadm 4096 Mar 20 06:48 ../
drwxrwxr-x 4 midadm midadm 4096 Mar 20 06:43 group_vars/
-rw-rw-r-- 1 midadm midadm  994 Mar 20 06:43 inventory.ini
```

#### 4.2 서버 정보 설정

group_vars 는 클러스터 설치에 필요한 설정 내용이 있고, inventory.ini 파일에는 설치 대상 서버들의 정보를 설정합니다 <br>

inventory.ini 파일에 설치 대상 서버 정보를 설정하겠습니다. <br>

```sh 
[all] # VM 인스턴스 내부 IP 설정 
instance-1 ansible_host=10.146.0.13 ip=10.146.0.13 etcd_member_name=etcd1
instance-2 ansible_host=10.146.0.14 ip=10.146.0.14 etcd_member_name=etcd2
instance-3 ansible_host=10.146.0.9  ip=10.146.0.9  etcd_member_name=etcd3
instance-4 ansible_host=10.146.0.10 ip=10.146.0.10
instance-5 ansible_host=10.146.0.11 ip=10.146.0.11

[kube-master] # 마스터 노드로 사용할 서버 호스트정보 설정 
instance-1
instance-2
instance-3

[etcd] # etcd 설치할 호스트네임 설정 
instance-1
instance-2
instance-3

[kube-node] #워커노드 호스트네임 설정 
instance-4
instance-5

[calico-rr]

[k8s-cluster:children]
kube-master
kube-node
calico-rr
```

#### 4.3 클러스터 구성 명령 실행 

서버 설정을 완료하였으므로 쿠버네티스 클러스터 구성 명령을 수행하겠습니다. <br>
아래 설정된 스크립트를 수행하면 되고 수행 시간은 약 20분 정도 소요됩니다.<br>

```sh 
cd /engn001/kubespray
ansible-playbook -i inventory/mycluster/inventory.ini -v --become --become-user=root cluster.yml
```

스크립트 수행이 완료 되었으면 아래 명령어 수행하여 모든 노드 상태를 확인합니다.

```sh 
root@instance-1:/engn001/kubespray# kubectl get node
NAME         STATUS   ROLES    AGE   VERSION
instance-1   Ready    master   33m   v1.16.8
instance-2   Ready    master   32m   v1.16.8
instance-3   Ready    master   32m   v1.16.8
instance-4   Ready    <none>   31m   v1.16.8
instance-5   Ready    <none>   31m   v1.16.8
```

### 5. 컨테이너 실행

쿠버네티스에서 컨테이너 실행 방법은 2가지가 있습니다.

- kubectl run 사용
- yaml 파일 사용

#### 5.1 kubectl run 사용

kubectl run 명령어로 파드 기본 컨트롤러인 deployment를 실행합니다. <br>

```sh 
kubectl run nginx-app --image nginx --port=80
```

| 항목 | 설명 |
| --- | --- |
| nginx-app | 디플로이먼트 이름 |
| --image nginx | 컨테이너 이미지 |
| --port=80 | 컨테이너 내부 네트워크에서 사용 할 포트 |

deployment 생성 확인을 위해 아래 스크립트를 수행합니다. <br>

```sh 
kubectl get pods 
kubectl get deployments
```

확인이 완료된 deployment 삭제를 위해 아래 스크립트를 수행합니다. <br>

```sh 
kubectl delete deployments mginx-app
```

#### 5.2 yaml 파일 사용

아래와 같이 yaml 파일을 작성합니다. <br>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
  labels:
    app: nginx-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-app
        image: nginx
        ports:
        - containerPort: 80
```

작성한 yaml 파일을 아래 스크립트로 실행합니다. <br>

```sh 
kubectl apply -f nginx-app.yaml
```

스크립트 수행 후 작동 확인 방법은 kubectl run 으로 deployment 생성 후 확인 방법과 같습니다.

### 6. 외부접속

쿠버네티스로 컨테이너 내부 네트워크는 외부와 격리 되어 있습니다. 외부에서 접속하기 위해서는 서비스
를 생성해야합니다. <br>

서비스 생성 방법은 아래와 같습니다. <br>

```sh 
kubectl expose deployment nginx-app --type=NodePort
```

| 항목 | 설명 |
| --- | --- |
| expose | 서비스 생성을 위한 명령어 |
| nginx-app | deployment 이름 |
| --type=NodePort | 서비스 유형으로 ClusterIP, NodePort, LoadBalancer, ExteralName <br> 등이 있음 |

서비스 생성 확인은 아래 스크립트 수행 또는 브라우저에서 http://localhost:port 로 접속하여 확인 가능합니다 <br>

```sh 
kubectl get service
kubectl describe service nginx-app
```