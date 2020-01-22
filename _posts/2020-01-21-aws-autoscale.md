---
title: "AWS Autoscaling"
date: 2020-01-21 11:00:00 +0800
categories: cloud
sidebar:
  nav: "newtech-sidebar"
---
# 1. AMI 생성
### 1.1 서비스 > EC2 선택
![EC2](/assets/images/autoscaling001.png)
### 1.2 AMI 대상 VM > 작업 > 이미지 > 이미지 생성 메뉴 선택
![Menu](/assets/images/autoscaling002.png)
### 1.3 이미지 > AMI > AMI 생성 확인
![Menu](/assets/images/autoscaling003.png)
# 2. Load Balancer 생성
### 2.1 로드 밸런싱 > 로드밴런서 > 로드 밸런서 생성
![Menu](/assets/images/autoscaling004.png)
### 2.2 Network Load Balancer 선택
![Menu](/assets/images/autoscaling005.png)

| 구분      | 항목                 | 입력값                                 |
| --------- | -------------------- | -------------------------------------- |
| 기본구성  | 이름                 | LB-AutoScale-Proxy                     |
|           | 체계                 | 내부                                   |
| 리스너    | 로드 밸런서 프로토콜 | TCP                                    |
|           | 로드 밸런서 포트     | 8109                                   |
| 가용 영역 | VPC                  | (기본값)                               |
|           | 가용 영역            | ap-northeast-2a (CIDR: 172.31.0.0/20)  |
|           |                      | ap-northeast-2b (CIDR: 172.31.16.0/20) |
|           |                      | ap-northeast-2c (CIDR: 172.31.32.0/20) |

### 2.3 보안 설정 구성 버튼 선택
![Menu](/assets/images/autoscaling006.png)
### 2.4 보안 리스너 설정
 - 프로토 타입이므로 보안 리스너 사용하지 않음
 ![Menu](/assets/images/autoscaling007.png)
 - 실제 적용 시, 리스너에 "TLS(보안 TCP)" 추가하여 사용
 ![Menu](/assets/images/autoscaling008.png)
### 2.5 라우팅 구성 버튼 선택
![Menu](/assets/images/autoscaling009.png)

| 구분      | 항목      | 설정 값            |
| --------- | --------- | ------------------ |
| 대상 그룹 | 대상 그룹 | 새 대상 그룹       |
|           | 이름      | LB-AutoScale-Group |
|           | 대상 유형 | 인스턴스           |
|           | IP        | 프로토콜(TCP)      |
|           |           | 포트(8109)         |
| 상태 검사 | 프로토콜  | TCP                |

### 2.6 대상 등록 선택(별다른 설정 없음)
![Menu](/assets/images/autoscaling010.png)
### 2.7 검토 > 생성 > 닫기 선택
![Menu](/assets/images/autoscaling011.png)