---
title: "TensorFlow - 04.TensorFlow for Java"
date: 2020-04-04 00:00:00 +0800
categories: tensorflow
tag: 
- tensorflow
- libtensorflow

sidebar:
  nav: "pe-sidebar"
---

다음은 [TensorFlow for Java 설치](https://www.tensorflow.org/install/lang_java?hl=ko)
에 대해 IntelliJ 환경에 실제 적용해 본 내용을 정리하였습니다.

### 1. 필요 요소 

 - libtensorflow.jar
 - JNI(Java Native Interface) 파일들
 
### 2. Maven 프로젝트 생성

- 먼저, Intellij환경에서 Java Maven Project를 생성

![TF](/assets/images/tensorflow/TF4001.png)

- Maven 선택 후 Next

![TF](/assets/images/tensorflow/TF4002.png)

- Project 정보 등록 후 Next

![TF](/assets/images/tensorflow/TF4003.png)

- Prjeoct Name, location 설정 후 Finish 

![TF](/assets/images/tensorflow/TF4004.png)

### 3. Denpendency 설정

- pom.xml 파일에 Tensorflow 사용을 위한 dependecy 설정을 추가

***pom.xml***

```xml 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.summit.tf</groupId>
    <artifactId>tf4java</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.tensorflow</groupId>
            <artifactId>tensorflow</artifactId>
            <version>1.14.0</version>
        </dependency>
    </dependencies>

</project>
```

### 4. JNI 다운로드

JNI(Java Native Interface)를 통해 JDK에서 다른 언어로 만들어진 TensorFlow API를 사용할 수 있음 <br>
JNI는 환경에 따라 아래 링크를 통해 다운로드 함 <br>

Linux CPU : [Linux JNI](https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow_jni-cpu-linux-x86_64-1.14.0.tar.gz) <br>
mocOS: [Mac JNI](https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow_jni-cpu-darwin-x86_64-1.14.0.tar.gz) <br>
Window CPU : [Window JNI](https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow_jni-cpu-windows-x86_64-1.14.0.zip) <br>

더 많은 자료는 [TensorFlow for Java 설치](https://www.tensorflow.org/install/lang_java?hl=ko) 에서 확인 <br>

다운로드 한 JNI를 압축 해제  그림 처럼 프로젝트 바로 아래 jni 디렉토리를 만들고 그 안에 복사함. <br>

![TF](/assets/images/tensorflow/TF4005.png)

### 5. 소스 구현

아래 예제를 작성 <br>

```java 
import org.tensorflow.Graph;
import org.tensorflow.Session;
import org.tensorflow.Tensor;
import org.tensorflow.TensorFlow;

public class HelloTensorFlow {
    public static void main(String[] args) throws Exception {
        try (Graph g = new Graph()){
            final String value = "Hello from " + TensorFlow.version();

            try(Tensor t = Tensor.create(value.getBytes("UTF-8"))){
                g.opBuilder("Const", "MyConst").setAttr("dtype", t.dataType()).setAttr("value", t).build();
            }

            try(Session s = new Session(g) ;
            Tensor output = s.runner().fetch("MyConst").run().get(0)) {
                System.out.println(new String(output.bytesValue(), "UTF-8"));
            }

        }

    }
}
```

### 6. VM 옵션 설정

Run/Debug Configurations 에서 VM 옵션에 JNI 경로를 아래와 같이 설정 <br>

![TF](/assets/images/tensorflow/TF4006.png)

### 7. 기동 및 결과 확인

일단 아래 이미지와 같은 메시지가 확인되면 성공 <br>

![TF](/assets/images/tensorflow/TF4007.png)
