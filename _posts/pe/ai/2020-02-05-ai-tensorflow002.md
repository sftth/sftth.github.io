---
title: "TensorFlow - 02.설치(on Mac)"
date: 2020-02-08 00:00:00 +0800
categories: tensorflow
sidebar:
  nav: "pe-sidebar"
---

### Python 설치 확인

Mac에는 기본적으로 Python이 설치되어 있습니다. 아래 명령어를 통해 파이선 버전을 확인 합니다.
 
- python2 버전 확인

    ```sh
    python --version
    Python 2.7.16
    ```

- python3 버전 확인 

    ```sh
    python3 --version
    Python 3.8.6
    ```

확인 결과 Python 3.5~3.8 이면 사용 가능 합니다.

### Python 수동 설치 
수동 설치된 Pyhon 버전을 변경하거나 다른 이유로 인해 수동 설치가 필요한 경우 아래 순서대로 진행합니다.

Python 설치를 위해 [https://www.python.org/](https://www.python.org/)에서 Downloads > Mac OS X 메뉴를 선택합니다.
<img src="/assets/images/tensorflow/tf101.png" width="600" height="584" alt="tf-insallation-mac">

설치 파일 중 원하는 버전의 macOS 64-bit installer를 선택합니다.
<img src="/assets/images/tensorflow/tf102.png" width="600" height="584" alt="tf-insallation-mac">

.pkg 파일을 다운로드 디렉토리에 저장합니다. <br>
<img src="/assets/images/tensorflow/tf103.png" width="600" height="584" alt="tf-insallation-mac">

저장된 .pkg 파일을 더블클릭하여 실행 후 표출되는 가이드에 따라 설치를 진행합니다. <br>
<img src="/assets/images/tensorflow/tf104.png" width="600" height="584" alt="tf-insallation-mac">

<img src="/assets/images/tensorflow/tf105.png" width="200" height="184" alt="tf-insallation-mac">
<img src="/assets/images/tensorflow/tf106.png" width="200" height="184" alt="tf-insallation-mac">
<img src="/assets/images/tensorflow/tf107.png" width="200" height="184" alt="tf-insallation-mac">
<img src="/assets/images/tensorflow/tf108.png" width="200" height="184" alt="tf-insallation-mac">
<img src="/assets/images/tensorflow/tf109.png" width="200" height="184" alt="tf-insallation-mac">
<img src="/assets/images/tensorflow/tf110.png" width="200" height="184" alt="tf-insallation-mac">
<img src="/assets/images/tensorflow/tf111.png" width="200" height="184" alt="tf-insallation-mac">

### pip package manager 업그레이드

다음으로 pip package manager를 20.0.2 버전으로 업그레이드가 필요합니다. 아래 과정을 통해 pip package manager 버전 확인 및 업그레이드를 수행합니다.

- pip3 설치 경로 권한 변경

    ```bash
    sudo chmod -R 775 /Library/Frameworks/Python.framework/
    ```
  
    **Info Notice:** Mac OS X에 기본으로 설치된 환경인 경우 Python.framework 디렉토리가 없는 경우가 있습니다. 
    이 경우는 위 작업을 제외합니다.
    {: .notice--info}

- pip3 최신 설치

    ```sh
    sudo curl https://bootstrap.pypa.io/get-pip.py | python3
    ```

- pip버전 확인
    
    ```sh 
    pip --version
    ```
- pip3.8 버전 확인

    ```sh 
    pip3.8 --version
    ```

    {: .notice--warning}
    ***Note*** pip 업그레이드 후, 버전 확인 시 원하는 버전이 표시되지 않거나 이전 버전이 보일 수 있습니다. 그것은 실제 업그레이드한 버전이 아닌 다른 버전의 python에 대한 정보일 수 있습니다. 
    이 경우 위 스크립트 처럼 tensorflow에 사용 할 python3.8.x에 종속된 pip3.8을 명시하여 버전을 확인해야 정확한 확인이 가능합니다.
    {: .notice--warning}

### 2.2 Install tensorflow
이제 mac 환경에 tensorflow를 위 기본적인 설치가 완료되었습니다. 다음 스크립트 실행하여 CPU 기반 Tensorflow를 설치합니다.

```sh 
summit@mac-pro ~ % pip install tensorflow
Collecting tensorflow
  Downloading tensorflow-2.3.1-cp38-cp38-macosx_10_14_x86_64.whl (165.2 MB)
     |████████████████████████████████| 165.2 MB 153 kB/s 
<중략>
Successfully installed 
<생략>
```
2020년 10월 부터 aiohttp 버전에 대한 호환성 이슈로 아래 메시지가 표출 될 수 있습니다.

```sh 
<중략>
ERROR: After October 2020 you may experience errors when installing or updating packages. This is because pip will change the way that it resolves dependency conflicts.

We recommend you use --use-feature=2020-resolver to test your packages with the new resolver before it becomes the default.

backend-ai-client 19.3.2 requires aiohttp~=3.4.0, but you'll have aiohttp 3.6.2 which is incompatible.

<생략>
```
이 경우 아래 명령어를 통해 호환성 테스트를 수행하고 Success 메시지가 표출된 경우 호환성에 문제가 없다고 판단하시고
계속 진행이 가능합니다.
```sh 
summit@mac-pro ~ % python3 -m pip install --upgrade pip

summit@mac-pro ~ % pip install example --use-feature=2020-resolver
<생략>
Successfully built example
Installing collected packages: example
Successfully installed example-0.1.0
```
다음으로 tf-nightly 설치합니다.

```sh 
summit@mac-pro ~ % pip install tf-nightly
Collecting tf-nightly
  Downloading tf_nightly-2.4.0.dev20200930-cp38-cp38-macosx_10_14_x86_64.whl (172.5 MB)
     |████████████████████████████████| 172.5 MB 9.7 MB/s 
<중략>
Successfully installed flatbuffers-1.12 tb-nightly-2.4.0a20201001 tf-estimator-nightly-2.4.0.dev2020100101 tf-nightly-2.4.0.dev20200930 typing-extensions-3.7.4.3
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

<script id="asciicast-FUB2qDxU3Y7TeHan0OSKTFq4d" src="https://asciinema.org/a/FUB2qDxU3Y7TeHan0OSKTFq4d.js" async></script>

***Note*** 위 스크립트 작성시 with tf.compat.v1.Session() as sess: 다음 라인부터 반드시
들여쓰기를 지켜야 합니다. 그렇지 않으면 "IndentationError: expected an indented block
에러가 발생합니다.
{: .notice--warning}

