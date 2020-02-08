---
title: "TensorFlow - 02.설치(on Mac)"
date: 2020-02-08 00:00:00 +0800
categories: tensorflow
sidebar:
  nav: "pe-sidebar"
---

## 1. Python Version
Mac에는 기본적으로 Python이 설치되어 있습니다. 따라서 아래 명령어를 통해 파이선 버전을 확인하고 버전이 3.5.x ~ 3.6.x 이면 사용 가능합니다.
Python 버전 확인 

```sh
#python 2 버전 확인
MacBook-Pro:~ $ python --version
Python 2.7.16
#python 3 버전 확인
MacBook-Pro:~ $ python3 --version
Python 3.6.1
```

{: .notice--warning}
***Note*** Mac에는 python 2.x 버전이 있을 수 있습니다. 이 경우 3.x 버전을 사용해야 하므로 명령어를 python3으로 실행합니다. 더불어
Mac에 python3.5.x ~ 3.6.x 버전이 없는 경우는 [TensorFlow - 01.설치(on Window)](https://sftth.github.io/ai-tensorflow001/) 참고하여
Mac에 설치합니다.
{: .notice--warning}

## 2. pip package manager
pip 20.0.2로 업그레이드가 필요하므로 pip package manager 버전 확인 및 업그레이드 합니다.

### 2.1 Latest pip

```bash
#pip3 설치경로의 권한 변경
MacBook-Pro:Frameworks $ sudo chmod -R 775 /Library/Frameworks/Python.framework/
#pip3 최신 설치
MacBook-Pro:~ $ sudo curl https://bootstrap.pypa.io/get-pip.py | python3
#pip3.6 버전 확인
MacBook-Pro:~ $ pip3.6 --version
```
{: .notice--warning}
***Note*** 여러 개의 python이 설치된 경우, pip 업그레이드 후, 버전 확인 시 혼란스러울 수 있습니다. 
원하는 버전이 표시되지 않거나 이전 버전이 보일 수 있습니다. 그것은 실제 업그레이드한 버전이 아닌 다른 버전의 python에 대한 정보일 수 있습니다. 
그래서 위 스크립트 처럼 tensorflow에 사용 할 python3.6.1에 종속된 pip3.6을 명시하여 버전을 확인해야 정확히 
tensorflow에 사용할 pip가 업그레드됐는지 확인이 가능하합니다.
{: .notice--warning}

### 2.2 Install tensorflow
이제 mac 환경에 tensorflow의 기본적인 설치가 완료되었습니다. 다음 스크립트 실행하여 CPU 기반 Tensorflow를 설치합니다.

```bash
# Current stable release for CPU and GPU
pip install tensorflow

# Or try the preview build (unstable)
pip install tf-nightly
```

## 3. Test
아래 Python 스크립트를 통해 설치된 TensorFlow를 검증합니다.

```sh 
import tensorflow as tf
with tf.compat.v1.Session() as sess:
  h = tf.constant("Hello")
  w = tf.constant("World")
  hw = h + w
  ans = sess.run(hw)
  print(ans)
```

- 참고 동영상

[![asciicast](https://asciinema.org/a/FUB2qDxU3Y7TeHan0OSKTFq4d.svg)](https://asciinema.org/a/FUB2qDxU3Y7TeHan0OSKTFq4d)


{: .notice--warning}
***Note*** 위 스크립트 작성시 with tf.compat.v1.Session() as sess: 다음 라인부터 반드시
들여쓰기를 지켜야 합니다. 그렇지 않으면 "IndentationError: expected an indented block
에러가 발생합니다.
{: .notice--warning}

