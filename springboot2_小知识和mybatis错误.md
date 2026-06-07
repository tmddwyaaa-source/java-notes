# springboot小知识和mybatis错误

## 对齐配置信息两方法

#### yml配置信息
```
email:
  user: jlakjd.com
  dode: jiangss
  host: smtp.qq.com
  auth: rrue
```

### @Value 和 @ConfigurationProperties
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix="email") //比@Value更便捷的对齐配置信息的方式或者说  配置信息的获取。要注意，由于是只确定前缀，所以使用时的类属性要对齐名称
                                            //格式是 @ConfigurationProperties(prefix=" 配置信息的前缀  就是第一个路径")
public class LianxiEm {
    @Value("${email.user}")     //对齐配置文件yml的属性信息，类才能使用正确的配置数据，账号密码信息等
                                //格式    @Value("${路径或者说键名}")
    public String user;

    @Value("${email.dode}")
    public String dode;

    @Value("${email.host}")
    public String host;

    @Value("${email.auth}")
    public String auth;
}
```

### mybatis小错误

关键包名的关系

    controller  业务层   前台小姐，告诉厨房客人要吃什么
    mapper      数据库映射层     厨师 ， 会做饭，但是只有几个，需要有人来指挥   执行sql，操作数据库        
    severice    业务层     厨房主管   管理mapper，分配人手，提高mapper的代码利用率
    impl         severice的实现类
    pojo         菜品摆放样式     和数据库表对象一一对应
    


controller        类关键注解 @RestController，标致了controller包

mapper             类关键注解 @Mapper        还要注意sql语句是否写对，写错不会有语法报错

Service           类关键注解 @Service  在实现类上写