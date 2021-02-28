---
title: k8s 기본-4.Replicaset(1)
excerpt: 컨트롤러 - Replicaset
date: 2020-03-22 00:00:00 +0800
last_modified_at: 2020-08-20 00:00:00 +0800
header:
  teaser: https://live.staticflickr.com/65535/50208492052_5430e07e89_b.jpg
  overlay_image: https://live.staticflickr.com/65535/50208492052_5430e07e89_b.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/65535/50208492052_5430e07e89_b.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: kubernetes  
---

컨트롤러는 파드를 관리하는 역할

### 레플리케이션 컨트롤러

- 지정된 숫자만큼 파드가 항상 클러스터 안에서 실행되도록 관리
- 예를 들어 파드 개수를 2로 명시한 레플리케이션 컨트롤러는 장애나 여러 이유로 파드 개수가 2보다 적으면
자동으로 2개로 맞춤(반대로 많아져도 2개만 실행되도록 조정)

### 레플리카세트

#### 정의

- 레플리케이션과 같은 역할을 수행 하지만 집합 기반(set-based)의 셀렉터(selector)를 지원
- 예를 들어 레플리케이션 컨트롤러에서 레이블을 선택할 때 등호(=)로 동일 여부를 확인하지만, 
레플리카세트는 in. notin, exists 같은 연산자를 지원하는 점이 다름

#### 레플리카세트 예시

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: whms-replicaset
spec:
  template: -----------------------> 어떤 파드를 실행할지 설정
    metadata:
      name: whms-replicaset-pod -----> 파드 이름
      labels:
        app: whms-replicaset-pod -----> 앱 컨테이너이고, 식별자는 whms-replicaset-pod이라고 설정
    spec:
      containers:
      - name: whms-replicaset-pod-con ---> 컨테이너 이름
        image: sftth/whms:latest --------------> 컨테이너 이미지
        ports:
        - containerPort: 8080 -------> 컨테이너 내부 포트
  replicas: 3 ---------------------> 리플리카세트가 유지할 파드 개수
  selector:
    matchLabels:
      app: whms-replicaset-pod -------> 관리할 파드의 레이블 (.spec.template.metadata.labels와 같음)
```

<script id="asciicast-Z2JnYlRGpf1Lzpegg5CkQmb4R" src="https://asciinema.org/a/Z2JnYlRGpf1Lzpegg5CkQmb4R.js" async></script>

#### 리플리카셋 삭제

- 리플리카셋 삭제시 파드도 같이 삭제 됨 <br>

```sh 
kubectl delete replicaset nginx-replicaset
```

- 파드는 남겨두고 리플리카셋만 삭제하려면 ***--cascade=false*** 옵션을 사용

```sh 
kubectl delete replicaset nginx-replicaset --cascade=false
```

<script id="asciicast-n1fxkL8atUmoTbRNIoeqfoqwi" src="https://asciinema.org/a/n1fxkL8atUmoTbRNIoeqfoqwi.js" async></script>

#### 레이블 수정

- replicaset이 관리하는 pod 중에 .metadata.labels.app 필드가 변경 되면 replicaset은
해당 pod를 관리 대상에서 제외하고 replicas 개수에 따라 새롭게 pod를 생성함
- 아래 커멘드는 pod 중에 whms-replicaset-5vv8c의 레이블을 변경 후 변경된 레이블 값 확인하기 위해
pod의 레이블 값을 출력해 보는 예제임

```sh 
[summit@gcp-minikube-vm template]$ kubectl get pods -o=jsonpath="{range .items[*]}{.metadata.name}{'\t'}{.metadata.labels}{'\n'}{end}"
whms-replicaset-5vv8c   map[app:whms-replicaset2-pod]
whms-replicaset-hsn5b   map[app:whms-replicaset-pod]
whms-replicaset-njz8m   map[app:whms-replicaset-pod]
whms-replicaset-xhzjc   map[app:whms-replicaset-pod]
[summit@gcp-minikube-vm template]$ 

```

### 3. 디플로이먼트
stateless 앱 배포 기능을 세분화<br>
### 4. 데몬세트
클러스터 전체 노드를 대상으로 특정 목적(로깅, 모니터링 등)을 위한 파드를 실행할 때 사용<br>
### 5. 스테이트풀세트
볼륨 등을 활용한 상태가 있는 파드를 관리<br>
### 6. 잡
실행된 후 종료해야 하는 성격의 작업<br>

### 7. 크론잡(cronjob)

- 역할

시간을 기준으로 주기적으로 반복 실행<br>

- cronjob 예제 템플릿

일반적인 크론 스케쥴 규칙에 따라 스케쥴 시간을 설정하고 스케쥴에 따라 수행될 컨테이너 정의 <br>

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *" -------------> 스케쥴 시간 설정
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox ------------> 실행 이미지 지정
            args: ---------------------> 실행 쉘 스크립트
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```
- cronjob 실행

```sh 
kubectl apply -f cronjob.yaml
```

- cronjob 스케쥴 설정 확인

```sh
kubectl get cronjobs
```

-  Job 확인 

```sh 
kubectl get jobs
```

- cronjob 삭제

```sh
kubectl delete cronjobs hello <-----크론잡이름
```

- 추가 옵션

***.spec.startingDeadlineSeconds*** <br>

지정된 시간에 크론잡이 실행되지 못했을 때 설정된 시간까지 기다렸다가 시간이 지나면
크론잡이 실행되지 않음 <br>

***.spec.concurrencyPolicy***

크론잡이 실행하는 잡의 동시성을 관리하고 설정 값으로 Allow, Forbid, Replace 등 있음.

```sh 
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello-concurrency
spec:
  schedule: "*/1 * * * *"
  startingDeadlineSeconds: 600
  concurrencyPolicy: Forbid
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster; sleep 6000
          restartPolicy: OnFailure
```

- 크론잡 실행

```sh 
kubectl apply -f cronjob-concurrency.yaml
```

- 크론잡 수정

.spec.concurrencyPlicy를 Allow, Forbid, Replace 로 각각 수정 <br>

```sh
kubectl edit cronjob hello-concurrency
```

- 크론잡 결과 확인

```sh 
kubectl get pods
```