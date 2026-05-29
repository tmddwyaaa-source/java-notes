# Bean

## Bean注册：

@Import: 注册一个类

@Bean: 注册一个具体的对象。      spring规定Bean不能单独漂流，需要用房子--->@Configuration把Bean罩起来

    那问题来了spring怎么知道这个房子？

所以要使用@Import找到这个类，
所以二者通常是一起使用，相辅相成

###### 注意

如果小房子@Configuration它在启动类目录下不用@Import大概率是没问题的

但是

如果在启动类范围外的话必须要使用@Import将小房子@Configuration拉进项目来
### Import:

```java
@SpringBootApplication
//@Import(CommonimportSelector.class)//注入配置类，参数是 xxx.class
//如果配置类过多使用CommonimportSelector集成配置类

@EnableCommonConfig  //自定义注解名，将@Import(CommonimportSelector.class)改名
public class BeanZhuceApplication {

    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(BeanZhuceApplication.class, args);
        //上面这句是获取对象 context 的    
        Country country = context.getBean(Country.class); //拿出IOC容器中bean对象
        System.out.println(country);

        System.out.println(context.getBean("province"));//拿出并打印
    }
}

```

#### 另类或者说企业级Bean配置用法:

使用流的方式读取配置文件.imports获取配置信息
##### CommonimportSelector类:
```java
public class CommonimportSelector implements ImportSelector {
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        //return new String[]{"com.ch.bean_zhuce.config.CommonConfig"};      //集成配置类，将使用配置信息放到大括号里面

        //企业级使用注入配置@import   先将配置信息放到.imports文件里面   再使用流接收
        List<String> imports = new ArrayList<>();
        InputStream is =CommonimportSelector.class.getClassLoader().getResourceAsStream("common.imports");
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        String line;
        try{
            while((line=br.readLine())!=null){  //一次读一行，.imports文件一行也只能写一个路径
                imports.add(line);
            }
        }catch(IOException e){
            throw new RuntimeException(e);
        }finally{
            if(br!=null){
                try{
                    br.close();
                }catch(IOException e){
                    throw new RuntimeException(e);
                }
            }
        }
        return imports.toArray(new String[0]);

    }
}
```

##### .imports配置文件:

.imports文件和application.yml同级
一行写一行配置信息
```
com.ch.bean_zhuce.config.CommonConfig
```

### Bean:

```java
@Configuration
public class CommonConfig {
    //注入country对象
    @Bean
    public Country country(){
        return new Country(); //没有属性，全是null
    }

    @Bean//("AA")       //Bean对象的默认名称是方法名，如果要改就在注解Bean加参数
    public Province province(Country country){//如果方法的内部需要使用IOC容器已经存在的Bean,
        System.out.println("province"+country);//则只需要方法参数声明即可，spring会自动注入
        return new Province();
    }
}
```

### 自定义注解:

```java
@Target(ElementType.TYPE)   //固定语法
@Retention(RetentionPolicy.RUNTIME) //固定语法
@Import(CommonimportSelector.class) //你要组合的注解
public @interface EnableCommonConfig {  //该注解文件的 方法名？ 就是自定义注解名称
}
```

