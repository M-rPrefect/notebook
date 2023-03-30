# JWT

JSON Web Token,通过数字签名的方式，以JSON对象为载体，在不同的服务器终端安全的传输信息，

一般使用于授权认证，一旦用户登录，后续每个请求都包含JWT，系统在每次处理用户请求之前，都要先进行JWT安全效验，通过后再进行处理。

# 组成

三部分

第一：Header

```java
{
    'typ':'JWT',
    'alg':'HS256'
}
```

第二：Payload

```java
{
    "sub":'1234567890',
    "name":'john',
    "admin":true
}
```

第三：Signature

```java
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);
var signature = HMACSHA256(encodedString,'secret');
```

# 生成token

```java
JwtBuilder jwtBuilder = Jwts.builder();
        HashMap<String, Object> jwtHeaderMap = new HashMap<>();
        jwtHeaderMap.put("typ","JWT");
        jwtHeaderMap.put("alg","HS256");
        // header
        String jwtToken = jwtBuilder.setHeaderParams(jwtHeaderMap)
        // playload
                .claim("username","chb")
                .claim("role","admin")
                .setSubject("admin-test")
                .setExpiration(new Date(System.currentTimeMillis()+time))
                .setId(UUID.randomUUID().toString())
                // signature
                .signWith(SignatureAlgorithm.HS256,signature)
                .compact();//拼接

```

# token解析

```java
String token = "eyJ0eXAiOiJKV1QiLC*";
        JwtParser parser = Jwts.parser();
        Jws<Claims> claimsJws = parser.setSigningKey(signature).parseClaimsJws(token);
        Claims body = claimsJws.getBody();
        System.out.println(body.get("username"));
        System.out.println(body.get("role"));
        System.out.println(body.getId());
```

# JWT工具类

```Java
package com.chb.mybatispluspro.system.config;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTCreator;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.interfaces.DecodedJWT;

import java.util.Calendar;
import java.util.Map;

/**
 * Created by Mr.Prefect on 2022/3/3
 * Author : 陈和斌
 * ClassName : JWTUtils
 * Date : 2022/3/3
 * Time : 9:58
 * Note :
 */
public class JWTUtils {

    // 签名
    private static final String SING = "^CHB@9%Q$^-^2217!&#";

    /**
    * @Description: 创建token
    * @Param: [map]
    * @ParamType: [java.util.Map<java.lang.String,java.lang.String>]
    * @return: java.lang.String
    * @Author: 陈和斌
    * @Date: 2022/3/3
    */
    public static String createToken(Map<String, String> map) {
        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.DATE, 15);
        //创建jwt builder
        JWTCreator.Builder builder = JWT.create();
        //payload
        map.forEach((k, v) -> {
            builder.withClaim(k, v);
        });
        String token = builder.withExpiresAt(instance.getTime())//过期时间
                .sign(Algorithm.HMAC512(SING)); //sign
        return token;
    }

    /** 
    * @Description: 验证token 
    * @Param: [token] 
    * @ParamType: [java.lang.String]
    * @return: void 
    * @Author: 陈和斌
    * @Date: 2022/3/3 
    */ 
    public static DecodedJWT verify(String token){
        return JWT.require(Algorithm.HMAC512(SING)).build().verify(token);
    }
}

```

使用拦截器处理token

```java
package com.chb.mybatispluspro.system.interceptor;

import com.chb.mybatispluspro.system.config.JWTUtils;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.HashMap;

/**
 * Created by Mr.Prefect on 2022/3/3
 * Author : 陈和斌
 * ClassName : JWTInterceptor
 * Date : 2022/3/3
 * Time : 15:01
 * Note :
 */
public class JWTInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 获取请求头中的token
        String token = request.getHeader("token");

        HashMap<String, Object> map = new HashMap<>();

        try {
            JWTUtils.verify(token);
            return true;
        }catch (Exception e){
            e.printStackTrace();
            map.put("msg","token无效!");
        }
        String json = new ObjectMapper().writeValueAsString(map);
        response.setContentType("application/json;charset=UTF-8");
        response.getWriter().println(json);
        return false;
    }
}

```

配置拦截器

```java
package com.chb.mybatispluspro.system.config;

import com.chb.mybatispluspro.system.interceptor.JWTInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * Created by Mr.Prefect on 2022/3/3
 * Author : 陈和斌
 * ClassName : InterceptorCofig
 * Date : 2022/3/3
 * Time : 15:22
 * Note :
 */
@Configuration
public class InterceptorCofig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new JWTInterceptor())
                .addPathPatterns("/system/student/**")
                .excludePathPatterns("/system/student/login");
    }
}

```

主要实践项目![image-20220303154156619](JWT.assets/image-20220303154156619.png)