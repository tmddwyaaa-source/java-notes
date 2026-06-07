# Springboot：


## 基础spring创建步骤：

1 创建maven项目

2 导入spring-boot-starter-web 起步依赖

3 编写Controller(给前端调用的)

4 提供启动类（main）
  
    而IDEA springboot直接帮我们简略了几步（1,2,4）从而大幅度提高了编写速度，但是并没有技术上的提高。

## 标准网址输入：  

    localhost:8080/你设定的端口名称(Controller里面)

## Controller:

```java
package com.ch.Jsb1.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/index")//设置网址端口
    public String index(){
        System.out.println("firstcontroller is running");
        return "welcome spring boot Application maven!";
    }
}
```

Application:  也就是启动程序

```java
package com.ch;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Jsb1Application
{
    public static void main(String[] args){
        SpringApplication.run(Jsb1Application.class,args);
        //输入网址 localhost:端口号/特殊字符串
    }
}
```

## 手动创建springboot:

1 创建maven项目
生成器选maven开头的

2 导入spring-boot-starter-web 起步依赖

3 编写Controller(给前端调用的)

4 提供启动类（main）


## 更改端口网址信息：

server.port=8081     改端口号
server.servlet.context-path=/start   改网址后缀

在yml配置文件中使用

## Springboot两种主要的属性配置方式：

一种是基础spring启动器自行生成的.prorerties

一种是手动执行的.yml。 

优点是层次清晰，能很明确的看到同级目录下的文件
  
优点2是更容易看到数据    也是目前主流的application创建格式

