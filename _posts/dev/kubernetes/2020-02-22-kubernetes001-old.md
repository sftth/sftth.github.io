---
title: Kubernetes 기본 01
date: 2020-02-22 00:00:00 +0800
excerpt: 쿠버네티스 설치
header:
  teaser: https://live.staticflickr.com/65535/50118270208_9de7c7ed9d_c.jpg
  overlay_image: https://live.staticflickr.com/65535/50118270208_9de7c7ed9d_c.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/65535/50118270208_9de7c7ed9d_c.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
#categories: kubernetes
---

## 개요
쿠버네티스 설치 방법은 간단한 방법에서 복잡한 방법까지 여러 가지가 있습니다. 간단한 방법은 개인PC에 운영체제에 맞게 구성하여 학습하는 용도로 활용할 수 있고 복잡한 
방법은 수고롭지만 직접 클러스터링 된 쿠버네티스를 구성하여 운영환경으로 활용할 수 있습니다. 아니면, 설치 없이 클라우드에서 제공하는 서비스로써 쿠버네티스를 이용하고 
이용에 대한 대가를 지불하는 방식도 가능합니다. 지금부터 아래 제시된 영역에 맞추어 쿠버네티스 설치방법을 설명합니다. 제시된 설치 방법 중 쿠버네티스를 활용하고자 하는 
목적에 맞는 부분을 취사선택하여 활용하면 되겠습니다.

| 항목                | 설명 |
| ---                | --- |
| 간단한 쿠버네티스 설치 방법 | 개인 PC등, 단일노드에 테스트 용으로 설치하는 방법 | 
| 서비스로 쿠버네티스 이용 방법 | AWS, GCP, Azure 등 클라우드 사업자가 제공하는 서비스로서의 쿠버네티스 이용 방법 |
| 복잡한 쿠버네티스 설치 방법 | 여러 노드에 클러스터링된 쿠버네티스 구성 방법 |

## 간단한 쿠버네티스 설치 방법
쿠버네티스를 경험하기 위해서는 쿠버네티스가 구성된 환경에 필요합니다. 우리에게는 보통 집에 개인용 데스크탑, 노트북 1대씩은 있어서 이 것을 이용하여 쿠버네티스를 
구성하고 싶을 때가 있습니다. 그래서 간단하게 쿠버네티스 기본 기능 정도를 학습해 볼 목적으로 말입니다. 이러한 목적으로 쿠버네티스를 1대의 노드에 설치하는 방법에는 
**Docker Desktop**을 이용하는 방법과 **Minikube**를 이용하는 방법이 있습니다. 각각 설치 방법을 알아보겠습니다.


### Docker Desktop을 통한 설치 방법

#### 설치환경 

| 항목                | 설명 |
| ---                | --- |
| OS | macOS Catalina v10.15.5 | 
| CPU | Intel Core i9 2.3GHz 8Core |
| Memory | DDR4 16GB 2667MHz |

#### 다운로드

도커 데스크탑[https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)에서 설치 파일을 
다운로드 합니다. 참고로 다운로드를 위해서 먼저 Sign In 버튼을 통해 가입 및 로그인을 해야 합니다. 그 후 OS에 따라 Stable 메뉴를 통해 설치 파일을 다운로드 
합니다.

![docker desktop](https://live.staticflickr.com/65535/50203076622_183b9b9f25_o.png)

#### 설치
다운로드한 Docker.dmg 파일을 더블클릭하면 아래와 같은 화면을 볼 수 있습니다. 좌측 Docker 이미지를 우측 Applications 이미지로 Drag & Drop하면 설치를
수행합니다. 설치 시 권한을 위한 사용자 계정 비밀번호를 묻는 과정이 있다는 것을 제외하고 화면에서 요구하는 데로 따라가 설치가 완료 됩니다.

![docker desktop](https://live.staticflickr.com/65535/50202362663_8ae5c356ec_o.png)

설치가 완료되면 노트북 상단에 도커 아이콘이 확인됩니다. 아이콘을 클릭하면 도커 데스크탑 패널이 나타나고 패널에 ***Docker Desktop is running***이 표시되어 
있으면 정상적으로 설치가 완료된 것입니다.

![docker desktop](https://live.staticflickr.com/65535/50202388993_bb58013a1c_o.png)

#### kubernetes 활성화

설치가 완료된 도커 데스크탑에서 쿠버네티스를 활성화합니다. 쿠버네티스를 활성화 하는 방법은 우선 방금 확인한 도커 데스크탑 패널에서 **Preferences...**를 선택합니다.

![docker desktop](https://live.staticflickr.com/65535/50202446233_18305b24d9_o.png)

표출된 설정 패널에서 순서에 따라 **Enable Kubernetes**,**Deploy Docker Stacks to Kubernetes by default**를 선택 후 Apply & Restart 
메뉴를 선택합니다.

![docker desktop](https://live.staticflickr.com/65535/50203250637_d5f5142a0b_o.png)

#### kubernetes 설치 확인

아래 영상을 참고하여 커멘트 입력을 통해 도커 데스크탑에서 kubernetes 활성화가 정상적으로 완료됐는 확인합니다.

<script id="asciicast-EtyVcEFxHPhWUhqJ2coFxJLMj" src="https://asciinema.org/a/EtyVcEFxHPhWUhqJ2coFxJLMj.js" async></script>

#### VM 생성

- VM 생성은 중첩 가상화가 가능한 VM이 필요하므로 아래 내용을 참고하여 생성


[참고: VM 인스턴스용 중첩 가상화 사용 설정](https://cloud.google.com/compute/docs/instances/enable-nested-virtualization-vm-instances#starting_a_nested_vm) <br>

- GCP CLOUD SHELL에서 아래 스크립트 실행

**Notice:** GCP CLOUD SHELL은 리눅스 VM 중, SSH 콤보박스의 gcloud 명령보기 메뉴를 선택하면 사용할 수 있고 최초 접속시 client의 pub key 등록이 필요하므로
ssh-keygen -t rsa -f ~/.ssh/[KEY_FILENAME] -C [USERNAME] 명령어로 미리 client에 rsa pub key를 만들어 둔다.
{: .notice--info}

- 수행 스크립트 

```sh 

# 디스크 생성
gcloud compute disks create disk1 --image-project centos-cloud --image-family centos-7 --zone us-central1-b

# 디스크 기반으로 이미지 생성
gcloud compute images create nested-vm-image \
  --source-disk disk1 --source-disk-zone us-central1-b \
  --licenses "https://compute.googleapis.com/compute/v1/projects/vm-options/global/licenses/enable-vmx"

# 이미지로 VM 생성
gcloud compute instances create gcp-minikube-vm --zone us-central1-b \
              --min-cpu-platform "Intel Haswell" \
              --image nested-vm-image

# VM 접속
gcloud compute ssh gcp-minikube-vm 
```

## 3. Dependency 설정

[참고](http://tonychungblogtest.blogspot.com/2017/10/to-remove-virtualbox-sudo-dpkg-list.html) <br>

- 필요한 패키지를 설치

```sh 
sudo yum update -y
sudo yum install wget
sudo yum install gcc make patch  dkms qt libgomp
sudo yum install kernel-headers kernel-devel fontforge binutils glibc-headers glibc-devel
```

- 재부팅

```sh 
sudo reboot
```

- 콘솔 재접속 후에 아래 명령어를 통해 새로운 커널로 기동 

```sh 
export KERN_DIR=/usr/src/kernels/`uname -r`
```

## 4. VirtualBox

```sh 
# yum repository 경로 등록을 위해 디렉토리 이동
[*** ]$ cd /etc/yum.repos.d/

# yum으로 virtualbox 설치를 위해 virtualbox repo를 등록
[*** yum.repos.d]$ sudo wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo

#virtualbox 설치
[*** yum.repos.d]$ sudo yum install VirtualBox-6.1
```

## 5. Minikube

```sh 
[*** yum.repos.d]$ sudo curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64   && sudo chmod +x minikube
[*** yum.repos.d]$ sudo mkdir -p /usr/local/bin/
[*** yum.repos.d]$ sudo install minikube /usr/local/bin/
```

### 6. Kubectl

```sh 
[*** yum.repos.d]$ sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
[*** yum.repos.d]$ sudo chmod +x ./kubectl
[*** yum.repos.d]$ sudo mv ./kubectl /usr/local/bin/kubectl

#kubectl 버전 확인 
[*** yum.repos.d]$ kubectl version --client
```

## 7. minikube 가상 머신 설치


```sh 
minikube start
```

## 8. 결과 화면

```sh 
[*** yum.repos.d]$ minikube start
😄  minikube v1.7.3 on Centos 7.7.1908
✨  Automatically selected the virtualbox driver
💿  Downloading VM boot image ...
    > minikube-v1.7.3.iso.sha256: 65 B / 65 B [--------------] 100.00% ? p/s 0s
    > minikube-v1.7.3.iso: 167.39 MiB / 167.39 MiB [] 100.00% 163.22 MiB p/s 2s
🔥  Creating virtualbox VM (CPUs=2, Memory=2000MB, Disk=20000MB) ...
🐳  Preparing Kubernetes v1.17.3 on Docker 19.03.6 ...
💾  Downloading kubelet v1.17.3
💾  Downloading kubeadm v1.17.3
💾  Downloading kubectl v1.17.3
🚀  Launching Kubernetes ...
🌟  Enabling addons: default-storageclass, storage-provisioner
⌛  Waiting for cluster to come online ...
🏄  Done! kubectl is now configured to use "minikube"
```

## 9. 동작 확인 

```sh 
kubectl get nodes
```
