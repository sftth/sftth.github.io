---
title: K8s 기본-10.Monitoring(2)
excerpt: Grafana 연동 
date: 2020-08-25 01:00:00 +0800
last_modified_at: 2020-08-25 00:00:00 +0800
categories: kubernetes
tag:
- kubernetes
header:
  teaser: https://live.staticflickr.com/65535/49986203617_8b099459c5_k.jpg
  overlay_image: https://live.staticflickr.com/65535/49986203617_8b099459c5_k.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/65535/49986203617_8b099459c5_k.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

### 개요 

- 수집 지표 정보를 분석 및 시각화하는 오픈소스 도구 
- 프로메테우스의 데이터를 grafana 연동하여 시각화 함 

### grafana pod 생성

```yaml
# grafana-deploy.yaml 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      name: grafana
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:latest
        ports:
        - name: grafana
          containerPort: 3000
        env:
        - name: GF_SERVER_HTTP_PORT
          value: "3000"
        - name: GF_AUTH_BASIC_ENABLED
          value: "false"
        - name: GF_AUTH_ANONYMOUS_ENABLED
          value: "true"
        - name: GF_AUTH_ANONYMOUS_ORG_ROLE
          value: Admin
        - name: GF_SERVER_ROOT_URL
          value: /
```

### grafana service 생성 
```yaml
# grafana-svc.yaml

apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
  annotations:
      prometheus.io/scrape: 'true'
      prometheus.io/port:   '3000'
spec:
  selector:
    app: grafana
  type: NodePort
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30004
```

### 배포 

```sh 
$ kubectl apply -f grafana-deploy.yaml
$ kubectl apply -f grafana-svc.yaml

$ kubectl get pod -n monitoring
NAME                                     READY   STATUS    RESTARTS   AGE
grafana-64cd46cb84-5qnlv                 1/1     Running   1          2d19h
node-exporter-kswxf                      1/1     Running   1          2d20h
node-exporter-p527j                      1/1     Running   1          2d20h
prometheus-deployment-78675675d9-qvtws   1/1     Running   1          2d20h
```

### datasource 추가 

- datasource에 prometheus 설정하여 연동 시킴
- Data Sources > Add data source 선택 <br>
  <img src="/assets/images/kubenetes/k8s-grafana001.png" width="600" height="584" alt="k8s-prometheus">

- Prometheus 선택 <br>
  <img src="/assets/images/kubenetes/k8s-grafana002.png" width="600" height="584" alt="k8s-prometheus">

- [Grafana](https://grafana.com) 접속 
- Grafana > Dashboard 이동 <br>
  <img src="/assets/images/kubenetes/k8s-grafana003.png" width="600" height="584" alt="k8s-prometheus">

- kubernetes 검색 > Copy ID to Clipboard 선택 <br>
  <img src="/assets/images/kubenetes/k8s-grafana004.png" width="600" height="584" alt="k8s-prometheus">

- grafana dashboard > DashBoard > Manage > Import 선택 <br>
  <img src="/assets/images/kubenetes/k8s-grafana005.png" width="600" height="584" alt="k8s-prometheus">

- 복사한 ID를 Import via grafana.com 붙여넣기 > Load 선택 <br>
  <img src="/assets/images/kubenetes/k8s-grafana006.png" width="600" height="584" alt="k8s-prometheus">

- Option 설정 > prometheus 선택 <br>
  <img src="/assets/images/kubenetes/k8s-grafana007.png" width="600" height="584" alt="k8s-prometheus">

- grafana dashboard 적용 화면 <br>
  <img src="/assets/images/kubenetes/k8s-grafana008.png" width="600" height="584" alt="k8s-prometheus">
