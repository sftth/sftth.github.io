---  
title: Python Ecosystem for ML #2
excerpt:  머신러닝 학습을 위한 환경 구성   
date: 2020-12-26 00:00:00 +0800
last_modified_at: 2020-12-26 00:00:00 +0800
categories: python
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

# 머신러닝 학습 환경 구성 방법

- 내 클라이언트에 패키지를 설치
- 가상환경 설정하기

## 내 클라이언트에 패키지를 설치

- 한 가지 프로젝트 진행 시 문제 없으나 여러개 진행 시 필요한 패키지 관리가 안 됨
- 설치 과정에서 버전 문제, 호환성 문제, 물리적인 환경상 제약으로 예기치 못한 문제가 작업 지연으로 작용

## 가상환경 설정하기

- 상대적 편의성 제공
- 프로젝트 별 패키지 관리 가능
- 호환성, 버전 문제 해결

# 가상환경 설정 방법

- virtualenv
- conda
- 둘다 나름 장/단점이 있으나 여기에서는 좀 더 대중적인 conda를 활용

# miniconda를 활용한 개발환경 구축  

## 개요
- 일반적으로 pyhon 기반 AI 학습에 사용하는 개발 환경을 구축하기 

## ANACONDA
- 과학 계산용 Python 통합 배포판
- [https://www.anaconda.com/](https://www.anaconda.com/)

## miniconda 
- 파이썬 가상 환경 관리 패키지 
- [공식 사이트](https://docs.conda.io/en/latest/miniconda.html)

## miniconda installation

1. chrome 브라우저 오픈 > [https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html) 접속
2. 링크 주소 복사 <br>
   https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
3. cd Downloads <br>
   wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
4. 결과

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
5. shell 스크립트 실행 

   ```sh 
   bash ./Miniconda3-latest-MacOSX-x86_64.sh   
   ```

## 그 밖에 알아야 할 도구 들 

### Jupyter
- 데이터 분석을 위한 파이썬 IDE

### pandas
- Python 데이터 분석 라이브러리

### NumPy
- 고성능 Array 처리 라이브러리

### matplotlib
- 데이터 시각화를 위한 파이썬 패키지

## 설치 

1. 가상환경 만들기 <br> 
   ```sh 
   conda create -n ml_scratch python=3.8 <br> 
   ```
   
   **참고** 환경 지우기: rm -rf ~/miniconda3/envs/ml_scratch
   {: .notice--info}

2. 가상환경 활성화 <br> 

   ```sh
   source activate ml_scratch
   ```
3. jupyter 설치 <br> 

   ```sh
   conda install jupyter
   ```
4. pandas 설치 <br> 

   ```sh
   conda install pandas
   ```
5. matplotlib <br> 

   ```sh
   conda install matplotlib
   ```

6. 설치 확인

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
## matplotlib 예제

- test_matplotlib.py 작성 

    ```sh
    import matplotlib.pyplot as plt
    plt.plot([1,2,3,4])
    plt.ylabel('some numbers')
    plt.show()
    ```
  
- miniconda 가상환경 실행

   ```sh 
   source activate ml_scratch
   ```
- python 파일 실행 

  ```sh 
  python test_matplotlib.py
  ```

## atom에 hydrogen plugin 설치 

1. conda 프로젝트 활성화

   ```sh 
   source activate ml_scratch
   ```

2. conda 명령어로 jupter를 설치

    ```sh 
    conda install jupyter
    ```
3. 커널 명령어 실행

   ```sh 
   python -m ipykernel install --user
   ```
4. jupyer notebook 실행

   ```sh 
   jupyter notebook
   ```
   
5. Kernel 확인
   ```sh 
   Jupyter 화면 > Kernel > Change kernel > Python3    
   ```
   
6. atom 실행

   ```sh 
   atom .
   ```
7. hydrogen 설치

- atom > preference > Install > Hydrogen > Install 