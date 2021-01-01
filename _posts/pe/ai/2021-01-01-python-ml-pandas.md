---  
title: Loading Data with Pandas 
excerpt:  Pandas 라이브러리를 통해 데이터 가져오기 예제    
date: 2021-01-01 00:00:00 +0800
last_modified_at: 2021-01-01 00:00:00 +0800
categories: tensorflow
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

## 가상환경 생성

```sh 
conda create -n ml_scratch python=3.8
source activate ml_scratch
```

## pandas 라이브러리 설치

```sh 
conda install pandas 
```

## jupyter notebook 실행

```sh 
jupyter notebook
```

## 예제 문법

```sh 
import pandas as pd #라이브러리 호출 
df_data = pd.read_csv(url, sep='\s+\, header=None) #csv 타임 데이터 읽기, separate는 공백, 헤더는 없음 
df_data.head() #처음 다섯줄 출력 
df_data.columns=['CRIM','ZN', .... 'MEDY'] #헤더 지정 
```

## 결과 화면 

<img src="/assets/images/ml/ml001.png" width="600" height="584" alt="ml">
