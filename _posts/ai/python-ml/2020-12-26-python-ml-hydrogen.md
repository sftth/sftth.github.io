---  
title: Python for ML-002 환경 구성#2
excerpt:  Python 머신러닝 학습을 위한 환경 구성   
date: 2020-12-26 00:00:00 +0800
last_modified_at: 2020-12-26 00:00:00 +0800
categories: python
header:
  teaser: /assets/images/01_teaser/wallpaper001.jpg
  overlay_image: /assets/images/01_teaser/wallpaper001.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/wallpaper001.jpg
  caption: "Photo by Max van den Oetelaar on Unsplash"
---

## 1. 개요
- Python 기반 머신러신 학습 환경이 잘 구성되었는지 예제 작성을 통해 확인함
- atom 스크립트 에디터(또는 개발도구라고 함)에 hydrogen을 설정하여 Python 코드 디버깅에 활용하는 방법 설명 

## 2. matplotlib 예제
- miniconda으로 생성한 가상 환경에서 간단한 matplotlib 예제를 수행하여 환경 구성이 잘 되었는지 확인 함 


### 2.1 miniconda 가상환경 활성화
- 실행 스크립트 
  ```sh 
  conda activate ml_scratch
  ```

### 2.2 테스트 스크립트 작성  
- test_matplotlib.py 작성 

  ```sh
  import matplotlib.pyplot as plt
  plt.plot([1,2,3,4])
  plt.ylabel('some numbers')
  plt.show()
  ```

### 2.3 스크립트 실행   
- python 파일 실행 

  ```sh 
  python test_matplotlib.py
  ```

## 3. atom에 hydrogen plugin 설치 

### 3.1 conda 가상환경 활성화
- 앞써 작성한 ml_scratch 가상환경 활성화 
  
   ```sh 
   source activate ml_scratch
   ```

### 3.2 jupter notebook 설치

- jupter notebook 설치(이미 설치된 상태이면 실행하지 않음)

    ```sh 
    conda install jupyter
    ```
### 3.3 커널 명령어 실행

   ```sh 
   python -m ipykernel install --user
   ```

### 3.4 jupyer notebook 실행

   ```sh 
   jupyter notebook
   ```
   
### 3.5 Kernel 확인

   ```sh 
   jupyter 화면 > Kernel > Change kernel > Python3    
   ```
   
### 3.6 atom 실행

   ```sh 
   atom .
   ```
### 3.7 hydrogen 설치
- atom > preference > Install > Hydrogen > Install 

### 3.8 사용 방법
- atom 열기 > 실행하고자하는 코드 마우스 드래그 > command + enter > 선택한 스크립트 실행 결과 확인