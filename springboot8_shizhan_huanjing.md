# 实战spring基础环境的搭建

## 基础步骤

### 1 创建数据库表 



### 2 创建项目注入基础依赖

web   starter   mysql驱动
```
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>4.0.1</version>
    </dependency>
    <dependency>
      <groupId>com.mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
    </dependency>
```

### 3 创建基础使用包 配置包 配置文件yml

controller   pojo  mapper 这样的基础包

resources配置包 

定义yml配置文件 ,       例如
````
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/big_event
    username: root
    password: Hzl123456!
````

### 4 定义启动类

```java
package com.ch;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class dashijianApplication
{
    public static void main(String[] args){
        SpringApplication.run(dashijianApplication.class,args);
    }
}

```