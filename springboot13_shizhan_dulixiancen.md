# 获取用户信息，独立线程传递信息

## 独立线程ThreadLocal的使用和理解

测试类Test测试基础
```java
public class ThreadLocalTest {

    @Test
    public void testThreadLocal(){
        ThreadLocal tl=new ThreadLocal();
        //ThreadLocal特点，线程独立，不会和普通线程一样被覆盖，每一个线程会创造一个平行宇宙，互不干扰
        new Thread(()->{
            tl.set("花花");
            for(int i=0;i<3;i++)
                System.out.println(Thread.currentThread().getName()+": "+tl.get());
        },"蓝色").start();
        new Thread(()->{
            tl.set("486");
            for(int i=0;i<3;i++)
                System.out.println(Thread.currentThread().getName()+": "+tl.get());
        },"天空").start();
    }
}
```

## 运用ThreadLocal实现一个信息的长期传递和复用

修改拦截器的实现类
```java
@Override       //实现接口必要实现的一个方法，如下
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("Authorization");  //通过头获得username
        try{
            Map<String, Object> claims = JwtUtil.parseToken(token);//解析令牌是否正确
            //v下面这句代码
            ThreadLocalUtil.set(claims);//将解析的用户信息传进ThreadLocal里，这里用的是课程提供的ThreadLocal工具类
            //A上面这句代码
            return true;         //令牌正确，放行
        }catch(Exception e){
            response.setStatus(401);    //错误响应编号
            return false;        //令牌错误，拦截
        }
    }
```

修改获取用户信息接口
```java
@GetMapping("/userInfo")//根据用户名查询用户信息
    public Result<User> userInfo(){
    
        Map<String,Object> map = ThreadLocalUtil.get();//将拦截器实现类里刚刚获取的用户部分信息放到Map集合里
        String um = (String) map.get("username");   //拿出集合里的名叫username的字符串
        
        User user = cjService.findByUserName(um);//查找对应
        return Result.success(user);//响应返回，注意返回的是一整个user，不是单个username
    }
```

为拦截器实现类添加新代码，关闭线程afterCompletion
```java
@Component      //将类注入到IOC容器中
public class Logininterceptor implements HandlerInterceptor {
    @Override       //实现接口必要实现的一个方法，如下
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("Authorization");  //通过头获得username
        try{
            Map<String, Object> claims = JwtUtil.parseToken(token);//解析令牌是否正确
            ThreadLocalUtil.set(claims);//将解析的用户信息传进ThreadLocal里，这里用的是课程提供的ThreadLocal工具类
            return true;         //令牌正确，放行
        }catch(Exception e){
            response.setStatus(401);    //错误响应编号
            return false;        //令牌错误，拦截
        }
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        ThreadLocalUtil.remove();//清理Thread线程防止处理过多内存泄漏
    }
}
```

展示一下ThreadLocal工具类
```java
public class ThreadLocalUtil {
    //提供ThreadLocal对象,
    private static final ThreadLocal THREAD_LOCAL = new ThreadLocal();

    //根据键获取值
    public static <T> T get(){
        return (T) THREAD_LOCAL.get();
    }
	
    //存储键值对
    public static void set(Object value){
        THREAD_LOCAL.set(value);
    }


    //清除ThreadLocal 防止内存泄漏
    public static void remove(){
        THREAD_LOCAL.remove();
    }
}
```