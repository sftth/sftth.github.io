---  
title: Jupyter Notebook Setup for ML 
excerpt:  머신러닝 학습을 위한 Jupyer Notebook 설치    
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

# jupyter Notebook 

## 개요 
- IPython 커널 기반 한 대화형 파이썬 쉘
- 일반적인 쉘 + 웹 기반 데이터 분석 Notebook 제공
- 미디어, 텍스트, 코드, 수식 등을 하나의 문서로 표현
- 사실상의 데이터 분석 interactive shell의 표준
- Julia + Python + R

## jupyter 실행

- workspace로 사용할 디렉토리 생성 

   ```sh 
   (ml_scratch)  ~/IDE/pyspace $ mkdir ml_scratch
   ```

- workspace로 이동 

   ```sh 
   (ml_scratch)  ~/IDE/pyspace $ cd ml_scratch
   ```

- jupyer notebook 실행

   ```sh 
   (ml_scratch)  ~/IDE/pyspace/ml_scratch $ jupyter notebook
   ```

## tensorflow 설치

   ```sh 
   pip install tensorflow
   ```
