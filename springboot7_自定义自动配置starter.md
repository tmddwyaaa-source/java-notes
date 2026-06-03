# 自定义自动配置

## springboot的自动配置原理
![img_1.png](img_1.png)

## 自定义starter

### 模块

一个独立的单元，有自己的代码和依赖，可以被其他项目单独引用

模块通常是 src包 + xml
    
一个项目通常由好几个模块组成，而不仅仅只是靠包类名来分割功能，仅靠类的话是不太能被单独引用的

#### autoconfigure

自动化配置的核心模块

现在核心目录下创建configure子包

```java

@AutoConfiguration
public class MybatisAutoConfig {

    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
        //使用时自需要在方法参数声明，spring会在启动后自动注入对象
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);//在引入JDBC这个起步依赖的它会自动注入dataSource-》数据源对象
        return sqlSessionFactoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(BeanFactory beanFactory){//beanFactory是所有存放Bean的容器
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        //扫描的包：启动类所在的包及其子包
        List<String> packages = AutoConfigurationPackages.get(beanFactory);//取启动类所在的包路径
        // 启动spring启动时会自动把源路径bean到bean工厂里面。       AutoConfigurationPackages是只取源路径的工具
        String p =packages.get(0);//启动类的包路径只有一个，0是只取唯一一个的意思
        mapperScannerConfigurer.setBasePackage(p);

        //扫描的注解
        mapperScannerConfigurer.setAnnotationClass(Mapper.class);

        return mapperScannerConfigurer;
    }
}
```

核心代码完成后要写入配置信息。

创建resources配置目录，再创建META-INF目录，再是子包spring（resources底下都是唯一不可更改的目录名称） 

再写入关键配置路径（唯一写法，不可更改）org.springframework.boot.autoconfigure.AutoConfiguration.imports   imports文件，再里面写入配置信息

如    com.ch.config.MybatisAutoConfig

#### 自定义starter

只是用来桥接autoconfigure核心代码的模块，保证核心代码不泄露

再写入xml依赖时除了注入autoconfigure依赖外，还要尽量写上autoconfigure的xml全部依赖，防止依赖传递出错。

这叫工程上的稳定性冗余