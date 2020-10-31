---
title: SpringCloudDataFlow 기초
excerpt: SCDF로 Spring Batch 배포하기 
date: 2020-09-23 01:00:00 +0800
last_modified_at: 2020-09-23 00:00:00 +0800
categories: springCloudDataFlow
header:
  teaser: https://live.staticflickr.com/33/65802815_f2f440a43f_h.jpg
  overlay_image: https://live.staticflickr.com/33/65802815_f2f440a43f_h.jpg
  overlay_filter: 0.5
  og_image: https://live.staticflickr.com/33/65802815_f2f440a43f_h.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

**참고:** [Deploy a Spring Batch application by Using Data Flow](https://dataflow.spring.io/docs/batch-developer-guides/batch/data-flow-spring-batch/)
{: .notice--info}

### 소스 빌드

- Sprinb Batch 샘플 소스를 maven 기반으로 빌드하여 로컬 레파지토리에 배포함 

**참고:** 개발 소스는 [Billrun Git](https://github.com/spring-cloud/spring-cloud-dataflow-samples/tree/master/dataflow-website/batch-developer-guides/batch/batchsamples/billrun)
에서 checkout 받을 수 있음 
{: .notice--info}

### 어플리케이션 등록 

- Apps 메뉴에서 방금 배포한 Jar를 등록함 
  <img src="/assets/images/scdf/scdf003.png" width="600" height="584" alt="csdf">
  
  <img src="/assets/images/scdf/scdf004.png" width="600" height="584" alt="csdf">
  
  <img src="/assets/images/scdf/scdf005.png" width="600" height="584" alt="csdf">
  
  <img src="/assets/images/scdf/scdf006.png" width="600" height="584" alt="csdf">

### 태스크 등록
- Tasks 메뉴에서 태스크를 등록함 

  <img src="/assets/images/scdf/scdf009.png" width="600" height="584" alt="csdf">
  
  <img src="/assets/images/scdf/scdf008.png" width="600" height="584" alt="csdf">

- 등록된 Task 기동 하기 

  <img src="/assets/images/scdf/scdf007.png" width="600" height="584" alt="csdf">

- Execution 탭에서 수행된 Task 목록을 확인할 수 있음 
  <img src="/assets/images/scdf/scdf010.png" width="600" height="584" alt="csdf">