# 文章分类添加 查询所有列表 查询指定列表

## 添加

### 管理层
新建CategoryController类
````java
@RestController
@RequestMapping("/category")
public class CategoryController {

    @Autowired
    private CategoryService categoryService;

    @PostMapping//添加文章分类
    public Result add(@RequestBody @Validated Category category){
        categoryService.add(category);
        return Result.success();
    }

    @GetMapping//查询所有文章分类
    public Result<List<Category>> list(){
        List<Category> cs = categoryService.list();
        return Result.success(cs);
    }

    @GetMapping("/detail")//查询指定文章分类
    public Result<Category> detail(int id){
        Category c =categoryService.findById(id);
        return Result.success(c);
    }
}
````

### 业务层
创建CategoryService类
```java
@Service
public class CategoryServiceImpl implements CategoryService{

    @Autowired
    private CategoryMapper categoryMapper;

    @Override
    public void add(Category category){
        category.setCreateTime(LocalDateTime.now());//将当前时间传入到category的对应属性
        category.setUpdateTime(LocalDateTime.now());

        Map<String,Object> map = ThreadLocalUtil.get();
        int userId = (int) map.get("id");//注这里的属性名要和实体类属性对应
        category.setCreateUser(userId);
        categoryMapper.add(category);
    }

    @Override
    public List<Category> list(){
        Map<String,Object> map = ThreadLocalUtil.get();
        int userid = (int) map.get("id");
        return categoryMapper.list(userid);
    }

    @Override
    public Category findById(int id){
        return categoryMapper.findById(id);
    }
}
```

### mapper层
创建CategoryMapper类
```java
@Mapper
public interface CategoryMapper {

    @Insert("insert into category(category_name,category_alias,create_user,create_time,update_time)" +
            "values(#{categoryName},#{categoryAlias},#{createUser},#{createTime},#{updateTime})")
    void add(Category category);

    @Select("select * from category where create_user = #{userid}")
    List<Category> list(int userid);

    @Select("select * from category where id = #{id}")
    Category findById(int id);
}
```

### 实体类注解限制输出结果

```java
@Data
public class Category {
    
    private Integer id;//主键ID
    @NotEmpty
    private String categoryName;//分类名称
    @NotEmpty
    private String categoryAlias;//分类别名
    private Integer createUser;//创建人ID
    @JsonFormat(pattern = "YYY-MM-DD HH:mm:ss")//JSON格式限制，限制成另一种时间格式
    private LocalDateTime createTime;//创建时间
    @JsonFormat(pattern = "YYY-MM-DD HH:mm:ss")
    private LocalDateTime updateTime;//更新时间
}
```