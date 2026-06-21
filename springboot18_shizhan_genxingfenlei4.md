# 更新文章分类和分组校验
---
## 更新文章分类篇

### 更新分类Controller
````java
@PutMapping//更新文章分类
    public Result update(@RequestBody @Validated Category category){
        categoryService.update(category);
        return Result.success();
    }
````

### Service

```java
@Override
    public void update(Category category){
        category.setUpdateTime(LocalDateTime.now());//参数是获取当前时间传给更新时间上
        categoryMapper.update(category);
    }
```


### Mapper

````java
@Update("update category set category_name = #{categoryName},category_alias = #{categoryAlias},update_time=#{updateTime} " +
            "where id = #{id}")
    void update(Category category);
````

### Category用户类
```java
@Data
public class Category {
    @NotNull
    private Integer id;//主键ID
    @NotEmpty
    private String categoryName;//分类名称
    @NotEmpty//非空，并且值也不能为空
    private String categoryAlias;//分类别名
    private Integer createUser;//创建人ID
    @JsonFormat(pattern = "YYY-MM-DD HH:mm:ss")//JSON格式限制，限制成另一种时间格式
    private LocalDateTime createTime;//创建时间
    @JsonFormat(pattern = "YYY-MM-DD HH:mm:ss")
    private LocalDateTime updateTime;//更新时间
}

```
---
## 分组校验

### 1定义分组项（接口）
```java
    public interface Add{//定义分组

    }
    public interface Update{

    }
    public interface test extends Default {//分组可以被继承，获得父类所有校验项

    }
```
### 2指定校验目标的分组

    注：当校验分组项为空时，默认是default分组
```java
    @NotNull(groups = Update.class)//定义指定分组
    private Integer id;//主键ID
    @NotEmpty(groups = {Add.class,Update.class})
    private String categoryName;//分类名称
    @NotEmpty(groups = {Add.class,Update.class})
    //非空，并且值也不能为空
    private String categoryAlias;//分类别名

    @NotNull  //当校验分组项为空时，默认是default分组
    private Integer createUser;//创建人ID
```

### 3校验时指定要校验的分组
```java
    @PostMapping//添加文章分类
    public Result add(@RequestBody @Validated(Category.Add.class) Category category){//校验时指定要校验的分组
        categoryService.add(category);
        return Result.success();
    }
```












