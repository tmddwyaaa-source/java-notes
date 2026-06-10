# 注册接口2

## 输入限制官方写法

#### 1 注入 Validation依赖
````
 <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
````

#### 2 在要限制的参数上写@Pattern ，指定效验规则
```java

@Validated      //别忘了给要进行的类名前加注解@Validated
public class CJController {
    @Autowired
    private CJServiceImpl cjService;
    
    @PostMapping("/register")
    public Result register(@Pattern(regexp = "^\\S(5,16)$")String username, @Pattern(regexp = "^\\S(5,16)$")String password){
        //使用@Pattern(regexp = 正则表达式-》"^\\S(范围A,范围B)$")要限制的是参数名
        User Cjuser = cjService.findByUserName(username);
        if(Cjuser==null){

            cjService.register(username,password);
             return Result.success();
        }else{
            return Result.error("用户名已被占用");
        }
    }
}
```
#### 3 在类名称前加入@Validated
```
@Validated
```

#### 4 在全局异常处理器处理参数效验失败的异常

````java
@RestControllerAdvice//@ControllerAdvice  作用：拦截所有controller抛出来的异常
//ResponseBody 作用：把返回值自动转化成JSON     而@RestControllerAdvice集成两者能力，拦截异常，返回JSON
public class GException {
    @ExceptionHandler(Exception.class)
    //@ExceptionHandler(特定异常类) 当Controller抛出特定异常后使用下面的方法
    public Result handleException(Exception e){
        e.printStackTrace();
        return Result.error(StringUtils.hasLength(e.getMessage())?e.getMessage():"不符合要求请重试");
        //判断获取的异常信息是否为空，为空则返回后面的，不然返回前面的        e.getMwssage-》获取当前异常信息
    }
}
````