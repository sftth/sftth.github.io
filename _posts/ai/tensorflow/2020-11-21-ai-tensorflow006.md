---  
title: TensorBoard 활용 
excerpt:  TensorBoard 활용하기  
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

### 설치 확인

TensorBoard 활용에 필요한 요소가 설치 되었는지 pip 명령을 통해 확인

```sh 
$ pip list
....
tensorboard            2.4.0
tensorboard-plugin-wit 1.7.0
tensorflow             2.3.1
tensorflow-estimator   2.3.0
termcolor              1.1.0
tf-nightly             2.4.0.dev20200930
...
```

### 샘플 코딩

${개발소스 Root Directory}/logs/fit 하위에 학습 로그를 남기도록 샘플 코드 작성

```python
import tensorflow as tf
import datetime

mnist = tf.keras.datasets.mnist

(x_train, y_train),(x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

def create_model():
  return tf.keras.models.Sequential([
    tf.keras.layers.Flatten(input_shape=(28, 28)),
    tf.keras.layers.Dense(512, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(10, activation='softmax')
  ])
model = create_model()
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])
#!!!!!! 학습 로그 남기는 위치
log_dir = "logs/fit/" + datetime.datetime.now().strftime("%Y%m%d-%H%M%S")
tensorboard_callback = tf.keras.callbacks.TensorBoard(log_dir=log_dir, histogram_freq=1)

model.fit(x=x_train,
          y=y_train,
          epochs=5,
          validation_data=(x_test, y_test),
          callbacks=[tensorboard_callback])
```

### tensorboard 실행

```sh 
$ tensorboard --logdir=./logs/fit/
Serving TensorBoard on localhost; to expose to the network, use a proxy or pass --bind_all
TensorBoard 2.4.0 at http://localhost:6006/ (Press CTRL+C to quit)
```

**Warning Notice:**  혹시 위 스크립트 입력 시 **ValueError: Duplicate plugins for name projector** 에러가 나면 
pip uninstall tensorboard tensorboard-plugin-wit 후 pip install tensorboard tensorboard-plugin-wit 수행 
{: .notice--warning}

### tensorboard 결과 화면

http://localhost:6006 접속 후 화면 확인 

<img src="/assets/images/tensorflow/tf601.png" width="600" height="584" alt="atom">

### 참고

tensorboard를 설치하지 않고 웹 기반으로 이용할 수 있는데 아래 링크 참조 

- [Tensorboard.dev](https://tensorboard.dev/)