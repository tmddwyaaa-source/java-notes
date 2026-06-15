# 拦截非指定接口1

## token令牌全局化检查拦截

### 拦截器拦截请求
实现类代码：
```java
import com.ch.utils.JwtUtil;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import java.util.Map;

//拦截器实现类
@Component      //将类注入到IOC容器中
public class Logininterceptor implements HandlerInterceptor {
    @Override       //实现接口必要实现的一个方法，如下
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("Authorization");  //通过头获得username
        try{
            Map<String, Object> claims = JwtUtil.parseToken(token);//解析令牌是否正确
            return true;         //令牌正确，放行

        }catch(Exception e){
            response.setStatus(401);    //错误响应编号
            return false;        //令牌错误，拦截
        }
    }
}

```

拦截器的配置类，前面指示拦截代码，没有指定不同网页的网址后缀的请求
```java
import com.ch.interceptors.Logininterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer{
    @Autowired
    private Logininterceptor logininterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(logininterceptor).excludePathPatterns("/user/login","/user/register");
        //这个类是拦截器配置类。              上面这句话是拦截所有请求.固定对象限制->拦截除了登陆注册外的所有请求
    }
}

```

## 获取用户信息接口
在基础的Controller管理层添加如下代码：
```java
@GetMapping("/userInfo")
public Result<User> userInfo(@RequestHeader(name = "Authorization")String token){//通过token令牌得到username
    Map<String,Object> map = JwtUtil.parseToken(token);//解析token
    String um = (String) map.get("username");//转化成字符串
    User user = cjService.findByUserName(um);//查找对应
    return Result.success(user);//响应返回，注意返回的是一整个user，不是单个username
}
```

    注意，获取用户信息时会导致所有的信息返回，甚至是用户密码，所以要做处理

在用户类user里使用注解
```java
@Data
public class User {
    private Integer id;//主键ID
    private String username;//用户名
    @JsonIgnore     //让springmvc把当前对象转换成JSON字符串时，忽略password
    private String password;//密码
    private String nickname;//昵称
    private String email;//邮箱
    private String userPic;//用户头像地址
    private LocalDateTime createTime;//创建时间
    private LocalDateTime updateTime;//更新时间
}

```

还有一件事，由于可能出现获取信息时工具请求获取的名称有问题比如

工具请求 create_time   而你的user里是createtime

所以可以使用驼峰命名，打开yml文件，使用特定配置信息

```
mybatis:
  configuration:
    map-underscore-to-camel-case: true  #开启驼峰命名  自动识别下划线命名并自动转换成非下划线
```
