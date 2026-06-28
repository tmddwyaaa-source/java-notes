# 文章查看详情更新删除接口
---
## 查看详情

### controller

```java
    @GetMapping("detail")//获取文章详情
    public Result<Article> detail(@RequestParam int id){
        Article at = articleService.detail(id);
        return Result.success(at);
    }
```

### Service

```java
    @Override
    public Article detail(int id) {
        Article at = articleMapper.detail(id);
        return at;
    }
```

### mapper

```java
    @Select("select * from article where id=#{id}")
    Article detail(int id);
```

---

## 更新

### controller

```java
    @PutMapping//更新文章
    public Result update(@RequestBody @Validated Article article){
        articleService.update(article);
        return Result.success();
    }
```

### Service

```java
@Override
public void update(Article article) {
    article.setUpdateTime(LocalDateTime.now());
    articleMapper.update(article);
}
```

### mapper

```java
    @Update("update article set title=#{title},content=#{content},cover_img=#{coverImg}," +
            "state=#{state},category_id=#{categoryId},update_time=#{updateTime} " +
            "where id = #{id}")
    void update(Article article);
```

---

## 删除

### controller

```java
    @DeleteMapping//删除文章
    public Result delete(@RequestParam int id){
        articleService.delete(id);
        return Result.success();
    }
```

### Service

```java
    @Override
    public void delete(int id) {
        articleMapper.delete(id);
    }
```

### mapper

```java
    @Delete("delete from article where id=#{id}")
    void delete(int id);
```
---
