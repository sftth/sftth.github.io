---
title: "Springboot Interceptor 설정"
date: 2020-01-28 11:00:00 +0800
categories: springboot
sidebar:
  nav: "dev-sidebar"
toc: true
---
# Session 확인을 위한 Interceptor 설정
## 1. 구현
 - 아래 패키지를 생성한다.
   - src/main/java/interceptor
 - 생성된 패키지 하위에 Interceptor 역할울 수행할 Class를 구현한다.

 ```java
 @Component
public class LoginInterceptor extends HandlerInterceptorAdapter {
    private static final Logger LOGGER = LoggerFactory.getLogger(LoginInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        if(LOGGER.isInfoEnabled()) {
        LOGGER.info("=======================<Interceptor Start>=======================");
            LOGGER.info("Request URL \t: " + request.getRequestURI());
        }

        SessionModel sessionModel = SessionManager.getUserInfo(request);

        if(null == sessionModel) {
            response.sendRedirect("/signIn");
            return false;
        }

        return super.preHandle(request, response, handler);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        if(LOGGER.isInfoEnabled()) {
            LOGGER.info("=======================<Interceptor End>========================\n");
        }
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object object, Exception arg3) throws Exception {
        if(LOGGER.isInfoEnabled()) {
            LOGGER.info("Interceptor > afterCompletion");
        }
    }
}
 ``` 
 - Class는 HanderInterceptorAdaptor를 상속하였다.
 - 상속한 Class의 메소드를 Override하여 각 단계별 처리하고자 하는 로직을 구현한다.
 - 여기에서는 preHandle에서 session을 갖는지 확인하는 로직을 담았다.
## 2. 설정
 - Interceptor를 등록하기 위해 Configuration 클래스를 작성한다.
 - 아래와 같이 작성하면 springboot 기동 시, Interceptor를 bean으로 등록한다.
 - 더불에 Interceptor에 필터링되는 URL에 대한 설정도 할 수 있다.

```java
@Configuration
public class WebMvcConfiguration implements WebMvcConfigurer {
    @Autowired
    @Qualifier(value = "loginInterceptor")
    private HandlerInterceptor interceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(interceptor)
                .addPathPatterns("/**")
                .excludePathPatterns("/signIn/**")
                .excludePathPatterns("/signInProcess/**")
                .excludePathPatterns("/css/**")
                .excludePathPatterns("/js/**")
                .excludePathPatterns("/error/**")
                .excludePathPatterns("/sample/**");
    }
}
```