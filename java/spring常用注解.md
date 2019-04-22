### @Service
用于标注业务层组件 
### @Controller
用于标注控制层组件（如struts中的action） 

#### @RestController
相当于@ResponseBody ＋ @Controller合在一起的作用

#### @Controller和@RestController的区别

1. 如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，或者html，配置的视图解析器 InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
2. 如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
     如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。

### @RequestMapping
设置请求路径

#### @GetMapping
设置get请求的路径，只允许get方式访问

#### @PostMapping
设置post请求的路径，只允许post方式访问

### @RequestBody
是作用在形参列表上，用于将前台发送过来固定格式的数据【xml 格式或者 json等】封装为对应的 JavaBean 对象，封装时使用到的一个对象是系统默认配置的 HttpMessageConverter进行解析，然后封装到形参上。

### @ResponseBody
表示该方法的返回结果直接写入 HTTP response body 中，而不是返回静态页或者jsp

### @Repository
用于标注数据访问组件，即DAO组件 
### @Component
泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。
### @EnableDiscoveryClient 
让服务使用eureka服务器
### @EnableFeignClients 
开启Feign功能
### @ConfigurationProperties 
设置从配置文件中读取的配置的前缀
参考org.springframework.cloud.config.client.ConfigClientProperties
### @ServletComponentScan 
在 SpringBootApplication 上使用@ServletComponentScan 注解后，
    Servlet、Filter、Listener 可以直接通过 @WebServlet、@WebFilter、@WebListener 注解自动注册，无需其他代码
### @Primary 
同一个接口两个实现类，当两个实现类都注册为spring bean的时候会报错，给一个加上@Primary，spring遇到这种情况就优先使用有@Primary的注册为bean
### @ComponentScan 
Spring bean扫描路径
### @PreDestroy
停服务时执行的方法

### @Bean
定义bean
可通过name属性定义bean的名称，@Autowired配合@Qualifier()，在Qualifier的value里设置bean的name来指定加载的bean
### @Configuration

