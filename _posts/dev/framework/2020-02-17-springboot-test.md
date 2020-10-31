---
title: "Springboot 구현 - Test"
date: 2020-02-05 00:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

다음은 springboot 환경에서 테스트 코드를 작성하는 기본적인 내용입니다.

### 의존성 확인

SpringBoot Test를 위해 의존성 설정 

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

**Info Notice:** test 관련 lib의 버전은 spring-boot-starter-parent 의 spring-boot-dependencies 에 설정
{: .notice--info}

### 기본 구성

SpringBoot 기본 구성 

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class LessonServiceTest {

}
```

### WebEnv: Mock

Web환경을 위해 Dispatchservlet을 Mockup으로 구동하기 위한 가장 쉬운 방법 

```java 
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
public class LessonServiceTest {
    @Autowired
    MockMvc mockMvc; <-- mock 객체 생성 

    @Test
    public void webTest() throws Exception{
        mockMvc.perform(get("/get/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("hello jacob"));
    }
}
```

### WebEnv: RANDOM_PORT
- mock이 아닌 실제 servlet이 기동 함 
- 테스트용 RestTemplate, 테스트용 WebClient 사용 필요
- Random Port를 사용하여 Mockup이 아닌 Dispatchservelt이 기동 됨

```java 
import...

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
public class LessonServiceTest {
    @Autowired
    TestRestTemplate testRestTemplate;

    @Test
    public void randomPortTest() throws Exception {
        String result = testRestTemplate.getForObject("/get/hello", String.class);
        assertThat(result).isEqualTo("hello jacob");
    }
}
```

- 테스트가 Service까지 가지 않고 Controller까지만 진행 방법
- Application Context Bean을 MocBean으로 교체 

```java 
    @Autowired
    TestRestTemplate testRestTemplate;

    @MockBean
    LessonService mockLessionService;

    @Test
    public void randomPortTest() throws Exception {
        when(mockLessionService.getName()).thenReturn("summit");

        String result = testRestTemplate.getForObject("/get/hello", String.class);
        assertThat(result).isEqualTo("hello summit");
    }
```

### WebTestClient

- web flux lib에 있는 RestClient 중 하나
- 기존 RestClient는 동기방식인 반면에 이 클래스는 비동기, 콜백 활용 처리
- 사용을 위해서는 dependency 추가 필요

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

- WebTestClient를 활용한 샘플 테스트 코드

```java 
    @Autowired
    WebTestClient webTestClient;

    @MockBean
    LessonService mockLessionService;

    @Test
    public void webClientTest() throws Exception {
        when(mockLessionService.getName()).thenReturn("summit");

        webTestClient.get().uri("/get/hello").exchange().expectStatus().isOk()
                .expectBody(String.class).isEqualTo("hello summit");

    }
```

## 5. 슬라이스 테스트

- @springbootTest는 @springBootApplication을 찾아감
- 모든 Bean scan 후 Mock Bean만 교체함
- 수 많은 Baen 등록을 피하고 필요한 영역으로 짤라서 테스트하는 것이 슬라이스 테스트
