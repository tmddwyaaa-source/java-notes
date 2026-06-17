# 修改用户密码

## 管理层新增代码

```java
@PatchMapping("/updatePwd")
public Result updatePwd(@RequestBody Map<String,Object> params){//接收JSON到指定对象，没有对象就用map接收
    String oldPwd = (String) params.get("oldPwd");//注意每次调用特定的接口是与传入数据的名称是完全对应的
    String newPwd = (String) params.get("newPwd");//比如这里是newPwd那传出去的数据名必须是newPwd
    String rePwd = (String) params.get("rePwd");

    if(!StringUtils.hasLength(oldPwd) || !StringUtils.hasLength(newPwd) || !StringUtils.hasLength(rePwd)){
        return Result.error("缺少基本的参数");
    }

    Map<String,Object> map = ThreadLocalUtil.get();
    String username = (String) map.get("username");//先通过ThreadLocal获取username
    User LoginUser = cjService.findByUserName(username);//再用用户名获取对应加密密码
    if(!LoginUser.getPassword().equals(Md5Util.getMD5String(oldPwd))){//将旧密码加密和记录的加密密码对比
        return Result.error("原密码错误");
    }

    if(!rePwd.equals(newPwd)){//注意是取反，要有！
        return Result.error("修改密码和确认密码不一致");
    }

    cjService.updatePwd(newPwd);//注意是取反，要有！
    return Result.success();
}
```

## 业务层新增代码

```java
@Override
public void updatePwd(String newPwd) {
    Map<String, Object> map = ThreadLocalUtil.get();
    int id = (int) map.get("id");
    cjMapper.updatePwd(Md5Util.getMD5String(newPwd), id);//将传入的密码加密
}
```

## Mapper层新增代码
```java
@Update("update user set password=#{newPwd},update_time=now() where id=#{id}")
void updatePwd(String newPwd,int id);
```
