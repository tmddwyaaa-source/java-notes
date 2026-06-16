# 修改用户信息

## 管理层的添加代码
```java
    @PutMapping("/update")
    public Result update(@RequestBody @Validated User user){
        cjService.update(user);//@Validated  启动校验注解的注解
        return Result.success();//@RequestBody  将传来的JSON数据转入user对象的，跟对象里的属性一一对应
    }
```
---
## 业务层的添加代码
````java
    @Override
    public void update(User user){
        user.setUpdateTime(LocalDateTime.now());  //LocalDateTime.now()获取修改信息的现在时间的关键词
        cjMapper.update(user);  //使用mapper方法
    }
````
---
## mapper的sql代码

```java
    @Update("update user set nickname=#{nickname},email=#{email},update_time=#{updateTime} where id=#{id}")
    void update(User user);
```

---
## 修改时的参数限制，在实体类里写注解
注意：是配合@Validated校验注解使用
```java
    @Data
    public class User {
    @NotNull//非空        //校验注解，需要在使用校验注解的地方使用，比如类的参数里-》class update
    private Integer id;//主键ID
    private String username;//用户名
    @JsonIgnore     //让springmvc把当前对象转换成JSON字符串时，忽略password
    private String password;//密码

    @NotEmpty//非空并且有值
    @Pattern(regexp="^\\S(1,10)$")//限制值的输入
    private String nickname;//昵称

    @NotEmpty//非空并且有值
    @Email//满足邮箱基本规定
    private String email;//邮箱
    private String userPic;//用户头像地址
    private LocalDateTime createTime;//创建时间
    private LocalDateTime updateTime;//更新时间
}
```

