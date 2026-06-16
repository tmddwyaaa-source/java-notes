# 修改用户头像

## 管理层新增代码

```java
    @PatchMapping("/updateAvatar")
    public Result updateAvatar(@RequestParam @URL String avatarUrl){//@URL校验头像,使它必须是个合法的url参数
        cjService.updateAvatar(avatarUrl);
        return Result.success();
    }
```

## 业务层新增代码

```java
    @Override
    public void updateAvatar(String avatarUrl) {
    Map<String,Object> map = ThreadLocalUtil.get(); //利用之前通过threadLocal存储用户信息，通过threadLocal获取id
    int id = (int) map.get("id");       //最后传到mapper哪里，sql语句使用
    cjMapper.updateAvatar(avatarUrl,id);
}
```

## Mapper层新增代码

```java
    @Update("update user set user_pic=#{avatarUrl},update_time=now() where id=#{id}")//使用now()方法获取当前时间
    void updateAvatar(String avatarUrl,int id);// 之前通过threadLocal存储用户信息，通过threadLocal获取id
```
