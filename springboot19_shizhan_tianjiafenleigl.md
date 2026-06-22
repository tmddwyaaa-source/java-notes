# 文章管理之添加，还有自定义注解校验

---

## 文章管理添加

### Controller

```java
@RestController
@RequestMapping("/article")
public class ArticleController {

    @Autowired
    private ArticleServiceImpl articleService;

    @PostMapping
    public Result add(@RequestBody @Validated Article article){//要注意使用@Validated启动注解
        articleService.add(article);
        return Result.success();
    }
}
```

### Service

```java
@Service
public class ArticleServiceImpl implements ArticleService {

    @Autowired
    private ArticleMapper articleMapper;
    @Override
    public void add(Article article) {
        article.setCreateTime(LocalDateTime.now());//获取当前时间到用户类
        article.setUpdateTime(LocalDateTime.now());
        Map<String,Object> map = ThreadLocalUtil.get();
        int userId = (int) map.get("id");
        article.setCreateUser(userId);//存入创作者id
        articleMapper.add(article);
    }
}
```

### Mapper

    注：当sql语句标签过多要更加仔细辨别，特别是符号

```java
@Mapper
public interface ArticleMapper {

    @Insert("insert into article(title,content,cover_img,state,category_id,create_user,create_time,update_time)" +
            "values(#{title},#{content},#{coverImg},#{state},#{categoryId},#{createUser},#{createTime},#{updateTime})")
    void add(Article article);
}
```

### 实体类属性限制

    注：注意两者差异
        @NotNull//可以给任何类型使用
    
        @NotEmpty//不能给int类使用

```java
@Data
public class Article {
    @NotNull//可以给任何类型使用
    private Integer id;//主键ID
    @NotEmpty//不能给int类使用
    @Pattern(regexp = "^\\S{1,10}$")
    private String title;//文章标题
    @NotEmpty
    private String content;//文章内容
    @NotEmpty
    @URL
    private String coverImg;//封面图像
    @State//自定义校验注解
    private String state;//发布状态 已发布|草稿
    @NotNull
    private Integer categoryId;//文章分类id
    private Integer createUser;//创建人ID
    private LocalDateTime createTime;//创建时间
    private LocalDateTime updateTime;//更新时间
}
```
---
## 自定义校验注解篇

### 1 定义自定义注解,如State

三个必备属性：message,groups,payload
```java
//自定义校验注解
@Documented//元注解
@Target({FIELD})//元注解
@Retention(RUNTIME)//注解的生命周期，参数是state生命终点。元注解
@Constraint(validatedBy = {StateValidation.class})//指定提供校验规则的类
public @interface State {
    //提供校验失败的展示信息
    String message() default "{State错误，State只能是草稿和已发布}";
    //指定分组
    Class<?>[] groups() default {};
    //负载 获取State注解的附加信息
    Class<? extends Payload>[] payload() default {};
}

```

### 2 创建指定校验规则类，如StateValidation

````java
public class StateValidation implements ConstraintValidator<State,String> {//参数<指定的注解,数据类型>
    @Override       //提供校验规则的类
    public boolean isValid(String value, ConstraintValidatorContext context){
        if(value == null){//校验规则
            return false;
        }
        if(value.equals("已发布") || value.equals("草稿")){
            return true;
        }
        return false;
    }
}
````

### 3 应用自定义校验注解

先在自定义注解的@Constraint放入校验规则参数

    @Constraint(validatedBy = {StateValidation.class})//指定提供校验规则的类

在要注解的地方使用自定义注解@State

```java
    @NotEmpty
    @URL
    private String coverImg;//封面图像

    @State//自定义校验注解
    private String state;//发布状态 已发布|草稿
   
    @NotNull
    private Integer categoryId;//文章分类id
```

















