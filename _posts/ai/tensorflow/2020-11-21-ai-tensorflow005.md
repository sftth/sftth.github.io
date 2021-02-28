---  
title: TensorFlow 개발 환경 구성
excerpt:  Tensor Flow 설치 파일 과 편집기 구성 관련 팁 정리 
date: 2020-11-21 00:00:00 +0800
last_modified_at: 2020-11-21 00:00:00 +0800
categories: tensorflow
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

# 개발 환경

## 설치 요소

아래 설치 요소를 vm(가상화)이 아닌 호스트에 직접 설치 함

- python 3.8.6
- pip 20.2.4
- tensorflow 2.3.0

## 설치 프로세스 

TensorFlow 개발 환경은 아래 포스트 참조

- [TensorFlow 2 설치 on Window](https://sftth.github.io/ai-tensorflow001/)
- [TensorFlow 2 설치 on Moc](https://sftth.github.io/ai-tensorflow002/)

---

# 편집기 

편집기로 Atom과 Pycharm을 선정하였고 이 중에 Atom을 사용하기로 함. <br>
Pycharm의 경우 Tensorflow 라이브러리 참조를 못 찾는 문제가 있어서 조치 후에 활용 하기로 함 <br>

## Atom 관련 설정 참고

### script 설치

Atom에서 Python을 실행시키기 위해서는 script 패키지 설치가 필요함

#### 설치 방법 
File > Settings > Install > script 설치 > Atom 재시작 

<img src="/assets/images/tensorflow/tf502.png" width="600" height="584" alt="atom">

### package 설치 

코딩 할 때 자동 완성 기능을 통해 개발 편의성을 갖기 위해 autocomplete 관련 패키지 설치 

<img src="/assets/images/tensorflow/tf503.png" width="600" height="584" alt="atom">

autocomplete-python의 경우 Settings 버튼을 클릭하여 Kite 도  설치해 줌 

<img src="/assets/images/tensorflow/tf504.png" width="600" height="584" alt="atom">

### python -> python3

Atom은 기본적으로 python을 실행하게 되어 있는데 로컬 환경에 python3.8.6버전이 python3으로 실행 되도록 설치 됨 <br>
그래서 Atom 개발 도구에서도 python3으로 실행되도록 설정을 변경함

#### 설정 메뉴

Atom > Preferences > Open Config Folder > .atom > packages > script > lib > grammars > python.coffee

<img src="/assets/images/tensorflow/tf501.png" width="600" height="584" alt="atom">
