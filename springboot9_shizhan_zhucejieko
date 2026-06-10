# 注册接口

一个快速set get 的小工具：lombok

先再xml依赖里面加入lombok依赖
```
 <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
    </dependency>
```

再给想要快速实现set get方法的类名称前写上@Data注解，当编译时会制动生成set get方法

## 主要流程
![img.png](img.png)

## 关键代码

### Controller

```java
@RestController
@RequestMapping("/user")
public class CJController {

    @Autowired
    private CJServiceImpl cjService;

    @PostMapping("/register")
    public Result register(String username, String password){
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

### Mapper
    注 ： sql语句别写错，写错不会有语法错误提示
```java
@Mapper
public interface CJMapper {
    @Select("select * from user where username=#{username}")
    User findByUserName(String usname);

    @Insert("insert into user(username, password, create_time, update_time) " +
            "values(#{username}, #{password}, now(), now())")
    void add(String username, String password);
}
```

### Service

```java
@Service
public class CJServiceImpl implements CjSerrvice {

    @Autowired
    private CJMapper cjMapper;

    @Override
    public User findByUserName(String username) {
        User u = cjMapper.findByUserName(username);
        return u;
    }

    @Override
    public void register(String username, String password) {

        String mdpassword = Md5Util.getMD5String(password);

        cjMapper.add(username,mdpassword);
    }
}
```

### 工具类Result  和  Md5

Result实现要返回的响应信息

Md5是注册进来的密码加密变成暗文存进数据库