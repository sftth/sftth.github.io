---
title: K8s 기본-10.Monitoring(1)
excerpt: Prometheus 
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

## 프로메테우스

### 개요

- 쿠버네티스 모니터링 도구 중 가장 주목 받고 있는 도구
- 시계열 데이터 저장, 다차원 데이터 모델
- PromQL이라는 쿼리 언어
- Pull or Push 유형의 데이터 수집 유형 지원 가능
- 시각화는 그라파나와 연결 가능

### 사용

#### 탬플릿 

- github에서 컨피그 용 탬플릿 다운로드 
```sh 
$ git clone https://github.com/prometheus/prometheus.git
$ cd /prometheus/documentation/examples/
$ mv prometheus-kubernetes.yml prometheus-kubernetes-config.yaml
```

#### 컨피그맵

- 컨피그맵 만들기
```sh 
kubectl create configmap prometheus-kubernetes --from-file=./prometheus-kubernetes-config.yaml
```

#### 디플로이와 서비스 설정 

- deployment & service 탬플릿 작성 및 반영하기 
```sh 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-app
#  namespace: monitoring
  labels:
    app: prometheus-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus-app
  template:
    metadata:
      labels:
        app: prometheus-app
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus
          args:
            - "--config.file=/etc/prometheus/prometheus-kubernetes-config.yaml"
#            - "--storage.tsdb.path=/prometheus/"
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: config-volume
              mountPath: /etc/prometheus/
            - name: storage-volume
              mountPath: /prometheus/
      volumes:
        - name: config-volume
          configMap:
            defaultMode: 420
            name: prometheus-kubernetes
  
        - name: storage-volume
          emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: prometheus-app
  name: prometheus-app-svc
  namespace: default
spec:
  type: LoadBalancer
  selector:
    app: prometheus-app
  ports:
  - protocol: TCP
    port: 9090
    targetPort: 9090
    nodePort: 30991
```

### 접속 

- GCP 환경에서 LoadBalancer 생성 
- Prometheus 서비스에 GCP의 LoadBalancer 할당
```sh  
$ kubectl patch svc prometheus-app-svc \
 -p '{"spec": {"type": "LoadBalancer", "externalIPs":["35.222.XXX.XX"]}}'
```

- https://IP:9090으로 접속<br>
  <img src="/assets/images/kubenetes/k8s-prometheus001.png" width="500" height="484" alt="k8s-prometheus">

### 문제

- EXECUTE 실행 시 아래 error 메시지 발생하면서 동작하지 않음

    ```sh 
    level=error ts=2020-09-03T14:47:56.639Z caller=klog.go:94 component=k8s_client_runtime func=ErrorDepth msg="/app/discovery/kubernetes/kubernetes.go:362: Failed to list *v1.Service: services is forbidden: User \"system:serviceaccount:default:default\" cannot list resource \"services\" in API group \"\" at the cluster scope"
    ```

- 문제 해결을 위해 지금까지 생성한 prometheus 관련 svc, deploy, configmap 모두 삭제

### 재설치 

참고 [https://gruuuuu.github.io/cloud/monitoring-02/#](https://gruuuuu.github.io/cloud/monitoring-02/#)

#### namespace

- namespace 생성 

    ```sh 
    $ kubectl create ns monitoring
    ```

#### cluster role

- prometheus-cluster-role.yaml

    ```yaml 
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRole
    metadata:
      name: prometheus
      namespace: monitoring
    rules:
    - apiGroups: [""]
      resources:
      - nodes
      - nodes/proxy
      - services
      - endpoints
      - pods
      verbs: ["get", "list", "watch"]
    - apiGroups:
      - extensions
      resources:
      - ingresses
      verbs: ["get", "list", "watch"]
    - nonResourceURLs: ["/metrics"]
      verbs: ["get"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRoleBinding
    metadata:
      name: prometheus
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: prometheus
    subjects:
    - kind: ServiceAccount
      name: default
      namespace: monitoring
    ```

#### configmap

- prometheus-config-map.yaml

    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: prometheus-server-conf
      labels:
        name: prometheus-server-conf
      namespace: monitoring
    data:
      prometheus.rules: |-
        groups:
        - name: container memory alert
          rules:
          - alert: container memory usage rate is very high( > 55%)
            expr: sum(container_memory_working_set_bytes{pod!="", name=""})/ sum (kube_node_status_allocatable_memory_bytes) * 100 > 55
            for: 1m
            labels:
              severity: fatal
            annotations:
              summary: High Memory Usage on 
              identifier: ""
              description: " Memory Usage: "
        - name: container CPU alert
          rules:
          - alert: container CPU usage rate is very high( > 10%)
            expr: sum (rate (container_cpu_usage_seconds_total{pod!=""}[1m])) / sum (machine_cpu_cores) * 100 > 10
            for: 1m
            labels:
              severity: fatal
            annotations:
              summary: High Cpu Usage
      prometheus.yml: |-
        global:
          scrape_interval: 5s
          evaluation_interval: 5s
        rule_files:
          - /etc/prometheus/prometheus.rules
        alerting:
          alertmanagers:
          - scheme: http
            static_configs:
            - targets:
              - "alertmanager.monitoring.svc:9093"
    
        scrape_configs:
          - job_name: 'kubernetes-apiservers'
    
            kubernetes_sd_configs:
            - role: endpoints
            scheme: https
    
            tls_config:
              ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
            bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    
            relabel_configs:
            - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
              action: keep
              regex: default;kubernetes;https
    
          - job_name: 'kubernetes-nodes'
    
            scheme: https
    
            tls_config:
              ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
            bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    
            kubernetes_sd_configs:
            - role: node
    
            relabel_configs:
            - action: labelmap
              regex: __meta_kubernetes_node_label_(.+)
            - target_label: __address__
              replacement: kubernetes.default.svc:443
            - source_labels: [__meta_kubernetes_node_name]
              regex: (.+)
              target_label: __metrics_path__
              replacement: /api/v1/nodes/${1}/proxy/metrics
    
    
          - job_name: 'kubernetes-pods'
    
            kubernetes_sd_configs:
            - role: pod
    
            relabel_configs:
            - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
              action: keep
              regex: true
            - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
              action: replace
              target_label: __metrics_path__
              regex: (.+)
            - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
              action: replace
              regex: ([^:]+)(?::\d+)?;(\d+)
              replacement: $1:$2
              target_label: __address__
            - action: labelmap
              regex: __meta_kubernetes_pod_label_(.+)
            - source_labels: [__meta_kubernetes_namespace]
              action: replace
              target_label: kubernetes_namespace
            - source_labels: [__meta_kubernetes_pod_name]
              action: replace
              target_label: kubernetes_pod_name
    
          - job_name: 'kube-state-metrics'
            static_configs:
              - targets: ['kube-state-metrics.kube-system.svc.cluster.local:8080']
    
          - job_name: 'kubernetes-cadvisor'
    
            scheme: https
    
            tls_config:
              ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
            bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
    
            kubernetes_sd_configs:
            - role: node
    
            relabel_configs:
            - action: labelmap
              regex: __meta_kubernetes_node_label_(.+)
            - target_label: __address__
              replacement: kubernetes.default.svc:443
            - source_labels: [__meta_kubernetes_node_name]
              regex: (.+)
              target_label: __metrics_path__
              replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
    
          - job_name: 'kubernetes-service-endpoints'
    
            kubernetes_sd_configs:
            - role: endpoints
    
            relabel_configs:
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
              action: keep
              regex: true
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
              action: replace
              target_label: __scheme__
              regex: (https?)
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
              action: replace
              target_label: __metrics_path__
              regex: (.+)
            - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
              action: replace
              target_label: __address__
              regex: ([^:]+)(?::\d+)?;(\d+)
              replacement: $1:$2
            - action: labelmap
              regex: __meta_kubernetes_service_label_(.+)
            - source_labels: [__meta_kubernetes_namespace]
              action: replace
              target_label: kubernetes_namespace
            - source_labels: [__meta_kubernetes_service_name]
              action: replace
              target_label: kubernetes_name
    ```

#### deployment

- prometheus-deployment.yaml

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: prometheus-deployment
      namespace: monitoring
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: prometheus-server
      template:
        metadata:
          labels:
            app: prometheus-server
        spec:
          containers:
            - name: prometheus
              image: prom/prometheus:latest
              args:
                - "--config.file=/etc/prometheus/prometheus.yml"
                - "--storage.tsdb.path=/prometheus/"
              ports:
                - containerPort: 9090
              volumeMounts:
                - name: prometheus-config-volume
                  mountPath: /etc/prometheus/
                - name: prometheus-storage-volume
                  mountPath: /prometheus/
          volumes:
            - name: prometheus-config-volume
              configMap:
                defaultMode: 420
                name: prometheus-server-conf
    
            - name: prometheus-storage-volume
              emptyDir: {}
    ```

#### node exporter

- prometheus-node-exporter.yaml

    ```yaml
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: node-exporter
      namespace: monitoring
      labels:
        k8s-app: node-exporter
    spec:
      selector:
        matchLabels:
          k8s-app: node-exporter
      template:
        metadata:
          labels:
            k8s-app: node-exporter
        spec:
          containers:
          - image: prom/node-exporter
            name: node-exporter
            ports:
            - containerPort: 9100
              protocol: TCP
              name: http
    ---
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        k8s-app: node-exporter
      name: node-exporter
      namespace: kube-system
    spec:
      ports:
      - name: http
        port: 9100
        nodePort: 31672
        protocol: TCP
      type: NodePort
      selector:
        k8s-app: node-exporter
    ```

#### service

- prometheus-svc.yaml

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: prometheus-service
      namespace: monitoring
      annotations:
          prometheus.io/scrape: 'true'
          prometheus.io/port:   '9090'
    spec:
      selector:
        app: prometheus-server
      type: LoadBalancer
      ports:
        - port: 8080
          targetPort: 9090
          nodePort: 30003
    ```

#### 적용

- kubectl apply 명령어 수행 

    ```sh
    $ kubectl apply -f prometheus-cluster-role.yaml
    $ kubectl apply -f prometheus-config-map.yaml
    $ kubectl apply -f prometheus-deployment.yaml
    $ kubectl apply -f prometheus-node-exporter.yaml
    $ kubectl apply -f prometheus-svc.yaml
    ```

- prometheus-svc 의 경우 GCP 환경에서 Loadbalancer를 생성하고 아래 명령어로 externalIP로 연결함

    ```sh 
    kubectl patch svc prometheus-service \
     -p '{"spec": {"type": "LoadBalancer", "externalIPs":["35.222.XXX.XXX"]}}'
    ```
- 접속 및 Execute 결과 화면 

  <img src="/assets/images/kubenetes/k8s-prometheus002.png" width="600" height="584" alt="k8s-prometheus">

### kube State Metrics 배포

- prometheus 가 모니터링하고 있는 타겟 중 kube-state-metric가 아직 Up 상태가 아님
- kube-state-metrics는 Pod 상태 정보를 모니터링하기 위해서 Up 상태여야 함

#### ClusterRole 탬플릿 

    ```yaml
    # kube-state-cluster-role.yaml
    
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: kube-state-metrics
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: kube-state-metrics
    subjects:
    - kind: ServiceAccount
      name: kube-state-metrics
      namespace: kube-system
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      name: kube-state-metrics
    rules:
    - apiGroups:
      - ""
      resources: ["configmaps", "secrets", "nodes", "pods", "services", "resourcequotas", "replicationcontrollers", "limitranges", "persistentvolumeclaims", "persistentvolumes", "namespaces", "endpoints"]
      verbs: ["list","watch"]
    - apiGroups:
      - extensions
      resources: ["daemonsets", "deployments", "replicasets", "ingresses"]
      verbs: ["list", "watch"]
    - apiGroups:
      - apps
      resources: ["statefulsets", "daemonsets", "deployments", "replicasets"]
      verbs: ["list", "watch"]
    - apiGroups:
      - batch
      resources: ["cronjobs", "jobs"]
      verbs: ["list", "watch"]
    - apiGroups:
      - autoscaling
      resources: ["horizontalpodautoscalers"]
      verbs: ["list", "watch"]
    - apiGroups:
      - authentication.k8s.io
      resources: ["tokenreviews"]
      verbs: ["create"]
    - apiGroups:
      - authorization.k8s.io
      resources: ["subjectaccessreviews"]
      verbs: ["create"]
    - apiGroups:
      - policy
      resources: ["poddisruptionbudgets"]
      verbs: ["list", "watch"]
    - apiGroups:
      - certificates.k8s.io
      resources: ["certificatesigningrequests"]
      verbs: ["list", "watch"]
    - apiGroups:
      - storage.k8s.io
      resources: ["storageclasses", "volumeattachments"]
      verbs: ["list", "watch"]
    - apiGroups:
      - admissionregistration.k8s.io
      resources: ["mutatingwebhookconfigurations", "validatingwebhookconfigurations"]
      verbs: ["list", "watch"]
    - apiGroups:
      - networking.k8s.io
      resources: ["networkpolicies"]
      verbs: ["list", "watch"]
    ```
#### service account 탬플릿

    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: kube-state-metrics
      namespace: kube-system
    ```
    
#### deployment 탬플릿 

    ```yaml
    # kube-state-deployment.yaml
    
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: kube-state-metrics
      name: kube-state-metrics
      namespace: kube-system
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: kube-state-metrics
      template:
        metadata:
          labels:
            app: kube-state-metrics
        spec:
          containers:
          - image: quay.io/coreos/kube-state-metrics:v1.8.0
            livenessProbe:
              httpGet:
                path: /healthz
                port: 8080
              initialDelaySeconds: 5
              timeoutSeconds: 5
            name: kube-state-metrics
            ports:
            - containerPort: 8080
              name: http-metrics
            - containerPort: 8081
              name: telemetry
            readinessProbe:
              httpGet:
                path: /
                port: 8081
              initialDelaySeconds: 5
              timeoutSeconds: 5
          nodeSelector:
            kubernetes.io/os: linux
          serviceAccountName: kube-state-metrics
    ```

#### service 탬플릿

    ```yaml
    # kube-state-svc.yaml
    
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        app: kube-state-metrics
      name: kube-state-metrics
      namespace: kube-system
    spec:
      clusterIP: None
      ports:
      - name: http-metrics
        port: 8080
        targetPort: http-metrics
      - name: telemetry
        port: 8081
        targetPort: telemetry
      selector:
        app: kube-state-metrics
    ``` 

#### 배포 
    
    ```sh 
    $ kubectl apply -f kube-state-cluster-role.yaml
    $ kubectl apply -f kube-state-deployment.yaml
    $ kubectl apply -f kube-state-svcaccount.yaml
    $ kubectl apply -f kube-state-svc.yaml
    
    $ kubectl get pod -n kube-system
    NAME                                  READY   STATUS    RESTARTS   AGE
    ...
    kube-state-metrics-6dc8f4d86d-c2gjp   1/1     Running   1          2d19h
    ```
    
