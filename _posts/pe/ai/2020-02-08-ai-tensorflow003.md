---
title: "TensorFlow - 03.Tutorial#1"
date: 2020-02-08 00:00:00 +0800
categories: tensorflow
sidebar:
  nav: "pe-sidebar"
---

이 튜토리얼은 TensorFlow의 이해를 돕고자 TensorFlow 사이트에 포스팅 된 [Beginner quickstart](https://www.tensorflow.org/tutorials/quickstart/beginner)
을 실제 수행한 내용을 담고 있습니다.

## 1. Environment
다음은 튜토리얼 수행을 위한 Python과 TensorFlow에 대한 버전 정보입니다.

| 항목 | 버전 | 설명 |
| --- | --- | --- |
| Python | 3.6.1 | TensorFlow2는 Python 3.5.x ~3.6.x를 권장 |
| TensorFlow | 2.2.0-dev20200207 | TensorFlow2 최신 버전 |

## 2. Python script
튜토리얼에서 실행 할 Python script 전체 내용입니다. 먼저 전체 내용을 확인하고 상세 내용을 설명하겠습니다.

```python
import tensorflow as tf
mnist = tf.keras.datasets.mnist

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

model = tf.keras.models.Sequential([
    tf.keras.layers.Flatten(input_shape=(28, 28)),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(x_train, y_train, epochs=5)

model.evaluate(x_test,  y_test, verbose=2)
```

## 3. Detail of script
다음은 Python 스크립트에 대한 설명입니다.

- 첫번째 구문은 TensorFlow library를 import 합니다.

```python
import tensorflow as tf
mnist = tf.keras.datasets.mnist
```
- 두번째 구문은 학습에 사용할 MNIST 데이터셋을 준비합니다. 더불어 샘플 값을 255로 나누어 정수에서 값이 작은 부동소수로 변환합니다.

```python
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0
```

- 다음은 keras를 활용하여 학습에 사용할 학습 모델을 작성합니다. <br>
- 학습 모델에는 입력값의 형태, 활성함수 등을 지정합니다. <br>
- 학습 모델에 대한 자세한 내용은 신경망 학습에 대한 이론이 필요하므로 이 포스트에서는 TensorFlow의 실행 결과를 보는 것으로 
하고 신경망학습에 대한 이론적 설명은 차후에 다루도록 하겠습니다.

```python
model = tf.keras.models.Sequential([
    tf.keras.layers.Flatten(input_shape=(28, 28)),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])
```

- 모델을 학습하고 학습 결과의 정확도를 평가합니다.

```python
model.fit(x_train, y_train, epochs=5)
model.evaluate(x_test,  y_test, verbose=2)
```

## 4. Result
- 총 60000개의 샘플을 학습하여 정확도를 향상 시키고 최종적으로 약 97%의 정확도를 나타냅니다. <br>
- 튜토리얼 실행 결과는 아래 동영상에서 확인이 가능합니다.

[![asciicast](https://asciinema.org/a/oeD9M85vF9knhbZwvLWzyPdCs.svg)](https://asciinema.org/a/oeD9M85vF9knhbZwvLWzyPdCs)

**Notice:** 튜토리얼을 수행 할 때 mac 에서는 python [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed 발생하는 경우가 있습다. 이 경우는 [https://krksap.tistory.com/1226](https://krksap.tistory.com/1226)를
참고하여 조치합니다.
{: .notice--info}

```sh
~/Applications/Python\ 3.6/Install\ Certificates.commnad
```