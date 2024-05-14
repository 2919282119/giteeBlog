---
title: springboot拦截器实现登录拦截
tags: 
- springboot
categories: 
---
在interceptor包下新建一个LoginInterceptor类，实现HandlerInterceptor接口，并重写preHandle方法。在preHandle方法中，可以对请求进行拦截和处理。
```java
@Component
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("Authorization");
        try {
            MyJwtUtil.parseToken(token);
            return true; // 如果解析成功，表示验证通过，继续执行
        } catch (Exception e) {
            response.setStatus(401); // 返回401 Unauthorized错误
            return false;// return false表示拦截请求，不继续执行
        }
    }
}

```
在webmvc配置类中，将LoginInterceptor添加到拦截器链中。
```java
@Configuration
public class WebMVCConfig implements WebMvcConfigurer {
    @Autowired
    private LoginInterceptor loginInterceptor;
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedHeaders("*")
                .allowedOrigins("*")
                .maxAge(1800)
                .allowedMethods("*");
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginInterceptor).excludePathPatterns("/user/login","/user/register");
    }
}


```