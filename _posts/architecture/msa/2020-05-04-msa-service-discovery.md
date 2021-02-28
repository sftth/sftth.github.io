---
title: "MSA - Service Discovery"
date: 2020-05-04 00:00:00 +0800
categories: msa
---

### 1. 정의
분산 아키텍처에서 시스템(서버, 서비스)의 물리적 위치 주소를 찾는것 <br>

### 2. 중요성

- MSA, CNCF는 서비스 인스턴스 개수를 신속하게 수평 확장 또는 축소
- 어플리케이션 회복성을 향상 시키기 위해 장애 서비스는 재빨리 제

**Info Notice:** CNCF: Cloud Native Computing Foundation
{: .notice--info}거

### 3. AS-IS(Load Balancer 기반)
- 서비스 위치 확인을 DNS와 Load Balancer로 처리했던 시절은 어떠 했을까?
- DNS를 호출하면 F5(http://f5.com, 상용)이나 HAProxy(http://haproxy.org, 오픈)로드 밸런서를 사용
- 서비스 인스턴스는 여러 서버에 배포 됐지만 어쨌든 서버의 개수는 정적(고정)이고 심지어 그 고정 상태가 영구적이기 까지하다.

### 4. TO-BE(MSA, CNCF)
- Load Balancer 기반은 다음 이유로 MSA, CNCF 환경에 적합하지 않다.
- 단일 장애 지점: 로드 밸런서가 죽으면 끝
- 수평 확장의 제약성: 로드 밸런서에서 기능적으로 지원 안되면 확장 못하고 심지어 라이선싱이 걸림돌
- 정적 관리: 서비스 경로 등록/취소가 느리고 공급업체 독점적 API가 제약
- 복잡성: 서비스 매핑 규칙 지정이 수동으로 정의하고 배포

- 하지만 MSA, CNCF 환경은 좀 다르다. 그래서 다른 서비스 디스커버리가 필요하다.

