---  
title: Jupyter Notebook Setup for ML 
excerpt:  머신러닝 학습을 위한 Jupyer Notebook 설치    
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

### 1. jupyter notebook 개요 
- IPython 커널을 기반으로 하는 대화형 파이썬 쉘
- 일반적인 쉘 + 웹 기반 데이터 분석 Notebook 제공
- 미디어, 텍스트, 코드, 수식 등을 하나의 문서로 표현
- 사실상의 데이터 분석 interactive shell의 표준
- Julia + Python + R
- 더 자세한 내용은 [jupyter 공식 사이트](https://jupyter.org/index.html)를 참고 

### 2. jupyter 실행

**참고** 본 내용은 [Python for ML-001 환경 구성#1](https://sftth.github.io/python/python-ml-ecosystem/), [Python for ML-001 환경 구성#2](https://sftth.github.io/python/python-ml-hydrogen/)
을 모두 숙지하고 있다는 가정하에 진행 
{: .notice--info}

- ml_scratch 가상환경 활성화
  ```sh 
  conda activate ml_scratch
  ```

- 작업 디렉토리 생성
  ```sh 
  (ml_scratch)  ~/IDE/pyspace $ mkdir ml_scratch
  ```

- 작업 디렉토리로 이동
  ```sh 
  (ml_scratch)  ~/IDE/pyspace $ cd ml_scratch
  ```

- jupyer notebook 실행
  ```sh 
  (ml_scratch)  ~/IDE/pyspace/ml_scratch $ jupyter notebook
  ```
  
- 실행 영상
  <script id="asciicast-cbkrTlvYnNhu9EduhY74CTK3n" src="https://asciinema.org/a/cbkrTlvYnNhu9EduhY74CTK3n.js" async></script>
  
### 3. jupyer notebook 기동화면

<img src="/assets/images/ml/ml006.png" width="600" height="800" alt="jupyter notebook">
