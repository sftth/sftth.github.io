---
title: "AWS Autoscaling - AWS 기본 설정"
date: 2020-01-21 11:00:00 +0800
categories: cloud
sidebar:
  nav: "it-sidebar"
---
AWS Autoscaling 설정은 크게 3가지로 구성된다. 이 포스팅은 AWS Autoscaling 설정을 위한 
가장 기본적인 3가지 과정에 관한 것이다.

## 1. AMI 생성
<p>
AMI 생성은 EC2 기동에 활용하는 이미지를 생성하는 작업이다.
</p>
  1. 서비스 > EC2 선택 <br>
  ![EC2](/assets/images/autoscaling001.png)
  
  2. AMI 대상 VM > 작업 > 이미지 > 이미지 생성 메뉴 선택
  ![Menu](/assets/images/autoscaling002.png)
  
  3. 이미지 > AMI > AMI 생성 확인
  ![Menu](/assets/images/autoscaling003.png)

## 2. Load Balancer 생성
<p>
Load Balancer는 Autoscaling 된 EC2 인스턴스(정확히는 내부 WEB,WAS 인스턴스)에
트래픽을 균형있게 분기해 주는 역할을 하므로 설정이 필요하다.
</p>

  1. 로드 밸런싱 > 로드밴런서 > 로드 밸런서 생성
  ![Menu](/assets/images/autoscaling004.png)
    
  2. Network Load Balancer 선택
  ![Menu](/assets/images/autoscaling005.png)
  
  3. NLB 설정 정보

     | 구분      | 항목                 | 입력값                                 |
     | --------- | -------------------- | -------------------------------------- |
     | 기본구성  | 이름                 | LB-AS                     |
     |           | 체계                 | 내부                                   |
     | 리스너    | 로드 밸런서 프로토콜 | TCP                                    |
     |           | 로드 밸런서 포트     | 8109                                   |
     | 가용 영역 | VPC                  | (기본값)                               |
     |           | 가용 영역            | ap-northeast-2a (CIDR: 172.31.0.0/20)  |
     |           |                      | ap-northeast-2b (CIDR: 172.31.16.0/20) |
     |           |                      | ap-northeast-2c (CIDR: 172.31.32.0/20) |

  4. 보안 설정 구성 버튼 선택
  ![Menu](/assets/images/autoscaling006.png)
    
  5. 보안 리스너 설정
     - 프로토 타입이므로 보안 리스너 사용하지 않음
     ![Menu](/assets/images/autoscaling007.png)
      
     - 실제 적용 시, 리스너에 "TLS(보안 TCP)" 추가하여 사용
     ![Menu](/assets/images/autoscaling008.png)

  6. 라우팅 구성 버튼 선택 <br>
  ![Menu](/assets/images/autoscaling009.png)
     - 설정 정보
  
     | 구분      | 항목      | 설정 값            |
     | --------- | --------- | ------------------ |
     | 대상 그룹 | 대상 그룹 | 새 대상 그룹       |
     |           | 이름      | LB-AutoScale-Group |
     |           | 대상 유형 | 인스턴스           |
     |           | IP        | 프로토콜(TCP)      |
     |           |           | 포트(8109)         |
     | 상태 검사 | 프로토콜  | TCP                |

  7. 대상 등록 선택(별다른 설정 없음) <br>
  ![Menu](/assets/images/autoscaling010.png)
 
  8. 검토 > 생성 > 닫기 선택 <br>
  ![Menu](/assets/images/autoscaling011.png)

## 3. AutoScaling 구성
  1. AUTO SCALING > 시작 구성 > 시작 구성 생성 버튼 선택
     - 내 AMI에서 "AMI-Tomcat-Redis" AMI 선택
     ![Menu](/assets/images/autoscaling012.png)
     - 인스턴스 유형 선택 > t2.micro 선택 > 세부 정보 구성 선택 <br>
     ![Menu](/assets/images/autoscaling013.png)
     - 설정 정보
  
     | 구분           | 항목      | 값                                   |
     | -------------- | --------- | ------------------------------------ |
     | 시작 구성 생성 | 이름      | Autoscale-Tomcat-Redis               |
     |                | 구매 옵션 | 스팟 인스턴스 요청 체크 안 함        |
     |                | IAM 역할  | 없음                                 |
     |                | 모니터링  | CloudWatch 세부 모니터링 활성화 체크 |
     | 고급 세부 정보 | -         | 프로토 타입으므로 default 설정 유지  |
      
     - 스토리지 추가 선택 <br>
     ![Menu](/assets/images/autoscaling014.png)

     - 보안 그룹 구성 선택 <br>
     ![Menu](/assets/images/autoscaling015.png)
     - 검토 > 시작 구성 생성 선택
  
  2. Auto Scaling 그룹 생성
  
     | 구분                    | 항목               | 값                                                |
     | ---------------------- | ------------------ | ------------------------------------------------- |
     | Auto Scaling 그룹 생성   | 그룹 이름          | Autoscaling-Tomcat-Redis-Group                    |
     |                        | 시작 구성          | Autoscaling-Tomcat-Redis                          |
     |                        | 그룹 크기          | 시작 개수: 2 인스턴스                             |
     |                        | 네트워크           | (기본값)                                          |
     |                        | 서브넷             | ap-northeast-2a, ap-northeast-2b, ap-northeast-2c |
     | 고급 세부 정보             | 로드 밸런싱        | 하나 이상의 로드 밸런서에서 트래픽 수신           |
     |                        | 클래식 로드 밸런서 | 미설정                                            |
     |                        | 대상 그룹          | LB-AutoScale-Group                                |
     | 나머지 설정               | -                  | 프로토 타입으므로 default 유지                    |

  
  3. 조정 정책 구성 선택 <br>
  ![Menu](/assets/images/autoscaling016.png)

     | 구분           | 항목                | 값                                |
     | -------------- | ------------------- | --------------------------------- |
     | 조정 정책 구성 | 조정 정책을 사용... | 선택                              |
     | 그룹 크기 조정 | 조정 범위           | 2~4                               |
     |                | 이름                | Scale Group Size                  |
     |                | 지표 유형           | 평균 CPU 사용률(유형은 변경 가능) |
     |                | 대상 값             | 임의 설정                         |
     |                | 인스턴스 필요 시간  | 300                               |
     |                | 축소 비활성화       | 미설정                            |

  4. 알림 구성은 미설정

  5. Tag 설정
     - Name : dev-was-autoscale 

  6. 검토 > Auto Scaling 그룹 생성 >  닫기 <br>
  ![Menu](/assets/images/autoscaling017.png)
