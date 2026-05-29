# Bean

## Bean扫描:

spring在启动时自动帮你管理类或者方法或者new    但需要你注册，就是那些注解@Import @Bean等等

## 启动类的扫描范围

springboot的启动类application里的注解@SpringbootApplication融合了@ComponentScan(<-指定扫描的范围，这个包内还是这个包外什么的)
扫描范围默认是application这个启动类所在包的范围。        超过范围就扫描不到，可能导致程序出错

如果一定要扫描默认范围外的文件就需要使用注解@ComponentScan(basePackages = " 路径 ")
