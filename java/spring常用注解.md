# @Service
用于标注业务层组件 
# @Controller
用于标注控制层组件（如struts中的action） 

## @RestController
相当于@ResponseBody ＋ @Controller合在一起的作用

## @Controller和@RestController的区别

1. 如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，或者html，配置的视图解析器 InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
2. 如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
     如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。

# @RequestMapping
设置请求路径

## @GetMapping
设置get请求的路径，只允许get方式访问

## @PostMapping
设置post请求的路径，只允许post方式访问

# @RequestBody
是作用在形参列表上，用于将前台发送过来固定格式的数据【xml 格式或者 json等】封装为对应的 JavaBean 对象，封装时使用到的一个对象是系统默认配置的 HttpMessageConverter进行解析，然后封装到形参上。

# @ResponseBody
表示该方法的返回结果直接写入 HTTP response body 中，而不是返回静态页或者jsp

# @Repository
用于标注数据访问组件，即DAO组件 
# @Component
泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。
# @EnableDiscoveryClient 
让服务使用eureka服务器
# @EnableFeignClients 
开启Feign功能
# @ConfigurationProperties 
设置从配置文件中读取的配置的前缀
参考org.springframework.cloud.config.client.ConfigClientProperties
# @ServletComponentScan 
在 SpringBootApplication 上使用@ServletComponentScan 注解后，
    Servlet、Filter、Listener 可以直接通过 @WebServlet、@WebFilter、@WebListener 注解自动注册，无需其他代码
# @Primary 
同一个接口两个实现类，当两个实现类都注册为spring bean的时候会报错，给一个加上@Primary，spring遇到这种情况就优先使用有@Primary的注册为bean
# @ComponentScan 
Spring bean扫描路径

```java
//单个
@ComponentScan(value = "io.mieux.controller")
//多个
@ComponentScan("com.package1,cn.package2")
//过滤
//只扫描带Controller注解的
//spring 默认会自动发现被 @Component、@Repository、@Service 和 @Controller 标注的类，并注册进容器中。要达到只包含某些包的扫描效果，就必须将这个默认行为给禁用掉（在 @ComponentScan 中将 useDefaultFilters 设为 false 即可）
@ComponentScan(value = "io.mieux", includeFilters = {@Filter(type = FilterType.ANNOTATION, classes = {Controller.class})},
        useDefaultFilters = false)
```

# @PreDestroy
停服务时执行的方法

# @Bean
定义bean
可通过name属性定义bean的名称，@Autowired配合@Qualifier()，在Qualifier的value里设置bean的name来指定加载的bean

## @Primary
@Primary注解的意思是在拥有多个同类型的Bean时优先使用该Bean，到时候方便我们使用@Autowired注解自动注入。

# @Configuration
## @EnableAutoConfiguration
这两个注解配合使用,EnableAutoConfiguration开启自动配置，Configuration设置配置
加Configuration的类里使用@Bean注解自动配置Bean

# @Scope
定义bean的作用域，spring里bean默认单例。
可通过属性加@Scope("prototype")，将其设置为多例模式

# 统一异常处理注解
spring的统一异常处理主要用到以下三个注解：
* @ExceptionHandler：统一处理某一类异常，从而能够减少代码重复率和复杂度
* @ControllerAdvice：异常集中处理，更好的使业务逻辑与异常处理剥离开
* @ResponseStatus：可以将某种异常映射为HTTP状态码

[参考文章](https://www.cnblogs.com/shuimuzhushui/p/6791600.html)

