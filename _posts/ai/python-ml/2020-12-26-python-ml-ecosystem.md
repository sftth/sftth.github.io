---  
title: Python for ML-001 환경 구성#1
excerpt:  Python 머신러닝 학습을 위한 가상환경 구성 및 필수 패키지 설치    
date: 2020-12-26 00:00:00 +0800
last_modified_at: 2021-03-01 00:00:00 +0800
categories: python
header:
  teaser: /assets/images/01_teaser/wallpaper001.jpg
  overlay_image: /assets/images/01_teaser/wallpaper001.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/wallpaper001.jpg
  caption: "Photo by Max van den Oetelaar on Unsplash"
---

## 1. 머신러닝 학습 환경 구성 방법
  Python 머신러닝 학습 환경 구성 방법은 다음 두 가지 방법을 고려할 수 있음 

- 머신러닝 학습 환경 구성 방법 

  | 방법 | 설명 |
  | --- | --- |
  | 내 클라이언트에 패키지 설치 | - 클라이언트 환경에 직접 설치 <br> - 클라이언트 리소스 전체를 머신러닝에 사용 <br> - 설치 될 패키지는 클라이언트 내에서 글로벌하게 사용 |
  | 가상환경 설정하기 | - 프로젝트마다 격리된 환경제공 <br> - 프로젝트 별 필요 패키지 설치가능 <br> - 프로젝트 별 관리 가능  |

### 1.1 내 클라이언트에 패키지 설치 특징
  "내 클라이언트에 패키지 설치" 방법은 PC에 패키지를 직접 설치하면서 관리측면, 호환성 측면, 다양성 측면에서 제약을 가짐 
  
- 내 클라이언트 패키지 설치 방법 특징 

  | 고려항목 | 특징 |
  | --- | ---|
  | 관리 측면 | 한 가지 프로젝트 진행 시 문제 없으나 여러 개 진행 시 필요한 패키지 관리가 안 됨 |
  | 호환성 측면 | - 이미 설치한 패키지의 버전과 프로젝트에서 필요한 버전과 상이한 경우 재설치 이슈 발생 <br> - 설치 과정에서 버전 문제, 호환성 문제, 물리적인 환경상 제약으로 예기치 못한 문제가 작업 지연으로 작용 |
  | 다양성 측면 | 클라이언트 내에 여러 개 프로젝트를 구성할 수 없음 | 

### 1.2 가상환경 설정하기
  "가상환경 설정" 방법은 상대적으로 장점을 제공하므로 이번 학습에서는 가상환경 설정 방법을 사용함

- 가상환경 설정 방법 특징 

  | 고려항목 | 특징 |
  | --- | ---|
  | 관리 측면 | 상대적 편의성 제공 |
  | 호환성 측면 |  호환성, 버전 문제 해결 |
  | 다양성 측면 | 프로젝트 별 패키지 관리 가능 | 

## 2. 가상환경 설정 방법
  "가상환경 설정" 방법에도 세 가지 정도 있고 모두 장/단점이 있으나 여기에서는 좀 더 대중적인 conda의 최소 설치 버전인 miniconda를 사용 

   **참고** 가상환경에는 [virtualenv](https://virtualenv.pypa.io/en/latest/), [anaconda](https://www.anaconda.com/products/individual), [miniconda](https://docs.conda.io/en/latest/miniconda.html) 가 있음  
   {: .notice--info}

## 3. miniconda 환경 구성  
 
  환경 구성 전에 pyhon 기반 AI 학습에 사용하는 Anaconda와 Miniconda의 차이는 아래와 같이 정의할 수 있음  

### 3.1 ANACONDA
  - 과학 계산용 Python 통합 배포판
  - OpenSource 버전에서 Commercial 버전까지 다양하게 존재
  - [공식 사이트](https://www.anaconda.com/)

### 3.2 miniconda
  - 파이썬 가상 환경 관리 패키지
  - A free minimal installer for conda 
  - [공식 사이트](https://docs.conda.io/en/latest/miniconda.html)

### 3.3 miniconda 설치

#### 3.3.1 설치 파일 다운로드

- [https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html) 접속
  
- 설치 파일 링크 주소 복사
  
  | 구분 | URL |
  | --- | --- |
  | MAC 용 | https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh |
  | Linux 용 | https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh |

- 설치 파일 다운로드

  ```sh 
  $ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
  --2020-12-19 00:49:02--  https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
  Resolving repo.anaconda.com (repo.anaconda.com)... 104.16.131.3, 104.16.130.3
  Connecting to repo.anaconda.com (repo.anaconda.com)|104.16.131.3|:443... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: 57112343 (54M) [application/x-sh]
  Saving to: ‘Miniconda3-latest-MacOSX-x86_64.sh’
  Miniconda3-latest-MacOSX-x86 100%[===========================================>]  54.47M  12.6MB/s    in 4.3s
  2020-12-19 00:49:06 (12.5 MB/s) - ‘Miniconda3-latest-MacOSX-x86_64.sh’ saved [57112343/57112343]
  ```

   **참고** wget이 없는 경우  MacOS: sudo brew install wget, CentOS: sudo yum install wget, Ubuntu: sudo apt-get install wget으로 설치 후 진행함 
   {: .notice--info}

#### 3.3.2 설치 스크립트 실행

- MacOS 환경 
  ```sh 
  bash ./Miniconda3-latest-MacOSX-x86_64.sh   
  ```
- Linux 환경
  ```sh 
  bash ./Miniconda3-latest-Linux-x86_64.sh   
  ```
- 실행 영상
  <script id="asciicast-b092YTZOVsdqJ2Vhuq21f2Xhw" src="https://asciinema.org/a/b092YTZOVsdqJ2Vhuq21f2Xhw.js" async></script>

## 4. 필수 패키지 설치

miniconda 설치가 완료되면 머신러닝 학습을 위한 필수 패키지를 설치 함 

### 4.1 설치 대상

- 설치 대상 목록표
  
  | 설치 대상 | 설명 |
  |---|---|
  | Jupyter | 데이터 분석을 위한 파이썬 IDE |
  | pandas  | Python 데이터 분석 라이브러리 |
  | NumPy   | 고성능 Array 처리 라이브러리 |
  | matplotlib | 데이터 시각화를 위한 파이썬 패키지 |

### 4.2 설치 

1. 가상환경 만들기
  ```sh 
  conda create -n ml_scratch python=3.8 
  ```

   **생성된 가상환경을 지우는 방법** rm -rf ~/miniconda3/envs/ml_scratch
   {: .notice--info}

2. 가상환경 활성화
  ```sh
  conda activate ml_scratch
  ```
3. jupyter 설치
  ```sh
  conda install jupyter
  ```
4. pandas 설치
  ```sh
  conda install pandas
  ```
5. matplotlib <br>
  ```sh
  conda install matplotlib
  ```
6. 설치 영상 
<script id="asciicast-SL16UttHsyWWdK09Fr4MYYtqe" src="https://asciinema.org/a/SL16UttHsyWWdK09Fr4MYYtqe.js" async></script>

7. 설치 확인

   ```sh 
   (ml_scratch)  summit@mac-pro  ~  python
   Python 3.8.5 (default, Sep  4 2020, 02:22:02)
   [Clang 10.0.0 ] :: Anaconda, Inc. on darwin
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import numpy
   >>> import pandas
   >>> import matplotlib
   >>> exit()
   ```
### 5. 마무리
 
- Python 기반 머신러닝 학습을 위해 miniconda 가상환경을 구성함
- Python 가상환경을 생성 및 전환하여 가상환경에 학습에 필요한 패키지를 설치하여 학습 환경 구성을 완료함