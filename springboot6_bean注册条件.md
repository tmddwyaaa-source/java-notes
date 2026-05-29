![img.png](img.png)
# Bean

## Bean注册注入属性:

如果要注入属性，可以使用@Value获取配置信息,但是要先在yml文件配置信息:
```
country:
  name: name
  system: system

```

```java
@Configuration
public class CommonConfig {

    //注入country对象
    @Bean
    public Country country(@Value("${country.name}") String name, @Value("${country.system}") String system) {
        //可以在方法参数里面使用@Value获取配置信息，缺点是不灵通，配置信息为空就报错要改代码
        Country country = new Country();
        country.setName(name);//也可以直接放""参数
        country.setSystem(system);//也可以直接放""参数
        return country;
        //return new Country(); 没有属性，全是null
    }
}
```

    你是否发现@Value不够聪慧，配置信息有问题不会主动停止读取配置信息，所以:

## Bean注册生效条件Conditional:

springboot提供了更便捷的几个注解，他们能有效的处理不同情况下是否生效

#### 注意

这些注解并没有主动注入的能力，它们只是类似if的条件注解    

### @ConditionalOnProperty

```java
//注入country对象
@ConditionalOnProperty(prefix = "country",name = {"name","system"})
//如果配置文件中配置了指定的信息就将属性注入，否则不注入
@Bean
public Country country(@Value("${country.name}") String name, @Value("${country.system}") String system) {
    //可以在方法参数里面使用@Value获取配置信息，缺点是不灵通，配置信息为空就报错要改代码
    Country country = new Country();
    country.setName(name);//也可以直接放""参数
    country.setSystem(system);//也可以直接放""参数
    return country;
}
```

### @ConditionalOnMissingBean

```java
    @ConditionalOnMissingBean(Country.class)//如果IOC容器中不存在country,则注入，否则不注入
    public Province province(){
        System.out.println("province");
        return new Province();
    }
```

### @ConditionalOnClass

```java
    @Bean
    @ConditionalOnClass(name="org.springframework.web.servlet.DispatcherServlet")
    //如果当前环境中存在起步依赖DispatcherServlet类，则注入Province,否则不注入
    //如果当前引入了web依赖，才会有DispatcherServlet,否则没有
    public Province province(){
        System.out.println("province");
        return new Province();
    }
```


