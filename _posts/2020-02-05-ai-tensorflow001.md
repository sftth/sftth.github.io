---
title: "TensorFlow2 - 01.설치"
date: 2020-02-05 00:00:00 +0800
categories: tensorflow
sidebar:
  nav: "pe-sidebar"
---
TensorFlow 활용을 위한 설치 가이드

## 1. Environment
TensorFlow2는 설치 환경 확인이 중요
- Python 3.5-3.7
- Window 7 or later
- macOS 10.12.6 (Sierra) or later (no GPU support) <br>

참고: [https://www.tensorflow.org/install?hl=ko](https://www.tensorflow.org/install?hl=ko)

## 2. Install Python

- Window용 Python 3.5.x 또는 Python 3.6.x 64bit를 설치 [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/)
- Pyhton.org의 Stable Releases는 3.8.1이지만 TensorFlow 호환성을 위해 Python 3.6.8 다운로드 <br>

![tensorflow](/assets/images/tensorflow002.png)

- Window에 Python3.8.1 64bit 설치

![tensorflow](/assets/images/tensorflow003.png)

- Python 버전 확인

```sh
C:\Windows\system32>python -V
Python 3.6.8
C:\Windows\system32>
```

<br>
TensorFlow 설치 방식은 여러가지가 있습니다. 다음으로 TensorFlow 설치 방식 중 pip 활용 방식과 Anaconda 활용 방식을
설명합니다.
<br>

## 3. Install TensorFlow2 - pip
- 아래 참고 URL에 명시되어 있는 pip 명령어를 차례대로 수행

![tensorflow](/assets/images/tensorflow007.png) <br>
참고: [https://www.tensorflow.org/install?hl=ko](https://www.tensorflow.org/install?hl=ko)

- pip upgrade 수행

![tensorflow](/assets/images/tensorflow005.png)

- pip 명령어를 통해 TensorFlow2 설치

![tensorflow](/assets/images/tensorflow006.png)

## 4. Install TensorFlow2 - Anaconda

### 4.1 Install Anaconda
- [Anaconda 다운로드](https://www.anaconda.com/distribution/)

- Python 3.7 version 선택

![tensorflow](/assets/images/tensorflow008.png)

- 다운로드 된 .exe 파일을 **관리자 권한으로 실행**

![tensorflow](/assets/images/tensorflow009.png)

- 설치가 완료되면 Anaconda Prompt를 **관리자 권한으로 실행**

![tensorflow](/assets/images/tensorflow010.png)

### 4.2 Install TensorFlow2

Anaconda 활용한 TensorFlow 설치는 3단계로 구성됨

- 1단계: pip 업데이트

```sh 
python -m pip install --upgrade pip
```

![tensorflow](/assets/images/tensorflow011.png)

- 2단계: tensorflow 생성

```sh 
conda create -n tensorflow python=3.6
```

![tensorflow](/assets/images/tensorflow012.png)

- 3단계: tensorflow 활성화

```sh
activate tensorflow

#프롬프트가 'tensorflow'로 바뀜
(tensorflow) > pip install tensorflow
```

![tensorflow](/assets/images/tensorflow013.png)

참고: [윈도우에 TensorFlow를 설치하는 쉬운 방법](https://brunch.co.kr/@mapthecity/15)

## 5. Test TensorFlow
- 다음 샘플 코드를 통해 Tensorflow 정상 동작 확인

```sh 
import tensorflow as tf
with tf.compat.v1.Session() as sess:
  h = tf.constant("Hello")
  w = tf.constant("World")
  hw = h + w
  ans = sess.rin(hw)
  print(ans)
```

{: .notice--warning}
***Note*** 위 스크립트 작성시 with tf.compat.v1.Session() as sess: 다음 라인부터 반드시
들여쓰기를 지켜야 합니다. 그렇지 않으면 "IndentationError: expected an indented block
에러가 발생합니다.
{: .notice--warning}


- Tensorflow 샘플 코드 동작 중 아래 에러 발생시 조치 필요

```sh
ImportError: Could not find the DLL(s) 'msvcp140.dll or msvcp140_1.dll' ...
```

에러 조치를 위해 다음 경로에서 DLL 다운로드 및 설치합니다. <br>

[DLL 다운로드 및 설치](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

![tensorflow](/assets/images/tensorflow014.png)

- 스크립트 실행 결과

```sh 
b'HelloWorld'
```

참고: [파이썬 텐서플로 AttributeError 문제 해결](https://blog.naver.com/websearch/221763828445)