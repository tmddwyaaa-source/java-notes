# Spring Boot 注解笔记

## 注解：

### 注解的意思:
给Java代码的标签 通常不具有参数，有例外

@Override:   意思是接下来的代码是重写上一代方法，比如继承父类 接口实现
@Autowired: 让springboot自动帮我new,并运行到代码中
@Service: 告诉springboot这个类是干活的核心代码
@GetMapping: 将方法跟网站绑定。有参数->网址后缀
@RequestParam(“参数”)： 从请求的URL里面取出特定的参数，赋给方法参数
```java
@RestController
public class UserControler {

    @Autowired
    private UserService userService;

    @GetMapping("/findById")
    public User findById(@RequestParam("id") Integer id) {
        System.out.println("==== 接口被调用了，id = " + id);
        return userService.findById(id);
    }
}
```

@Select("select * from user where id = #{id}")：
sql语句，其中#{ 值 }是占位符，把方法参数‘值‘添加到sql里面

@Mapper: 该接口方法对应sql
@RestController: 该类处理HTTP请求
```java
@Mapper
public interface UserMapper {

    @Select("select * from user where id = #{id}")
    public User findById(Integer id);
}
```


## Mybatis步骤：
创建数据库表
先确定依赖， mybatis 和 Mysql  
配置信息获取  .yml确定mysql地址账号密码
创建并实现接口UserService：
```java
@Service
public class UserServiceiml implements UserService {
    @Autowired
    private UserMapper userMapper;

    @Override
    public User findById(Integer id){
        return userMapper.findById(id);
    }
}
```

创建对应数据库表属性的类，要有基本方法和构造方法，还有重写toString方法：
```java
   @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", gender=" + gender +
                ", phone='" + phone + '\'' +
                '}';
    }
```

定义一个mapper接口，它是sql的使用接口，里面有SQL的使用方法
```java
@Mapper
public interface UserMapper {

    @Select("select * from user where id = #{id}")
    public User findById(Integer id);
}
```

定义一个controller，它是和网页交互的接口：
值得注意的是由于版本迭代，新版springboot不会自动获取URL参数，要使用@RequestParam手动获取URL参数到方法参数里，注解在方法参数里使用
```java
@RestController
public class UserControler {

    @Autowired
    private UserService userService;

    @GetMapping("/findById")
    public User findById(@RequestParam("id") Integer id) {
        System.out.println("==== 接口被调用了，id = " + id);
        return userService.findById(id);
    }
}
```
