---
title: jwt实现登录校验
tags: 
- springboot
categories: 
---
先在pom.xml中引入jwt依赖
```xml
    <dependency>
        <groupId>com.auth0</groupId>
        <artifactId>java-jwt</artifactId>
        <version>4.3.0</version>
    </dependency>
```
utils包下新建JwtUtil类
```java
public class MyJwtUtil {
    public static final  String JWT_SECRET = "xxxxxxxx";
    public static String geneToken(Map<String, Object> claims){
        return JWT.create()
                .withClaim("claims",claims)
                .withExpiresAt(new Date(System.currentTimeMillis()+60*60*1000*24))
                .sign(Algorithm.HMAC384(JWT_SECRET));
    }
    public static Map<String,Object> parseToken(String token){
        return JWT.require(Algorithm.HMAC384(JWT_SECRET))
                .build()
                .verify(token)
                .getClaim("claims")
                .asMap();
    }
}


```
在登录拦截器中，从请求头中获取token，并解析token，判断用户是否登录