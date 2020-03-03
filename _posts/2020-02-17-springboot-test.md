---
title: "Springboot 구현 - Test"
date: 2020-02-05 00:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
---

다음은 springboot 환경에서 테스트 코드를 작성하는 기본적인 내용입니다.

## 1. 의존성 확인

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

**Info Notice:** test 관련 lib의 버전은 spring-boot-starter-parent 의 spring-boot-dependencies 에 설정
{: .notice--info}

## 2. WebEnv: Mock

- Web환경을 위한 Dispatchservlet을 Mockup으로 구동

```java 
import...

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment =  SpringBootTest.WebEnvironment.MOCK)
@ActiveProfiles("test")
@AutoConfigureMockMvc
public class SampleControllerTest {
    /**
     * MockMvc 활용 예제
     * @AutoConfigureMockMvc 추가 필요
     *
     */
    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        mockMvc.perform(get("/sample/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("hello Jacob"))
                .andDo(print());
    }
```

## 3. WebEnv: RANDOM_PORT

- 테스트용 RestTemplate, 테스트용 WebClient 사용 필요
- Random Port를 사용하여 Mockup이 아닌 Dispatchservelt이 기동 됨

```java 
import...

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment =  SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test")
public class SampleControllerTest {
    /**
     * TestRestTemplate 활용 예제
     */
    @Autowired
    TestRestTemplate testRestTemplate;

    @MockBean
    SampleService mockSampleService;

    @Test
    public void hello() throws Exception {
        when(mockSampleService.getName()).thenReturn("Summit");

        String result = testRestTemplate.getForObject("/sample/hello", String.class);
        assertThat(result).isEqualTo("hello Summit");
    }
}
```
## 4. WebTestClient

- web flux lib에 있는 RestClient 중 하나
- 비동기, 콜백 활용 처리
- 사용을 위해서는 dependency 추가 필요

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

## 5. 슬라이스 테스트

- @springbootTest는 @springBootApplication을 찾아감
- 모든 Bean scan 후 Mock Bean만 교체함
- 수 많은 Baen 등록을 피하고 필요한 영역으로 짤라서 테스트하는 것이 슬라이스 테스트
